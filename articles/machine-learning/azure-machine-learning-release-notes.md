---
title: Wat is er nieuw in de release?
titleSuffix: Azure Machine Learning
description: Meer informatie over de nieuwste updates voor Azure Machine Learning en de machine learning en de python-Sdk's voor gegevens voorbereiding.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: BlackMist
ms.date: 02/18/2021
ms.openlocfilehash: ebd4aed284869eb74760de8612a76139d26d47f5
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502345"
---
# <a name="azure-machine-learning-release-notes"></a>Opmerkingen bij de release Azure Machine Learning

In dit artikel vindt u meer informatie over Azure Machine Learning releases.  Ga voor de volledige SDK-referentie-inhoud naar de hoofd pagina van de hand leiding van de Azure Machine Learning van de [**SDK voor python**](/python/api/overview/azure/ml/intro) .

__RSS-feed__: ontvang een melding wanneer deze pagina wordt bijgewerkt door de volgende URL in uw feedlezer te kopiëren en plakken: `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes%22&locale=en-us`

## <a name="2021-02-28"></a>2021-02-28
### <a name="azure-machine-learning-studio-notebooks-experience-february-update"></a>Ervaring met Azure Machine Learning Studio notitie blokken (update voor februari)
+ **Nieuwe functies**
  + [Systeem eigen terminal (ga)](https://docs.microsoft.com/azure/machine-learning/how-to-access-terminal). Gebruikers hebben nu toegang tot een geïntegreerde Terminal en een Git-bewerking via de geïntegreerde Terminal.
  + [Notitieblok fragmenten (preview-versie)](https://azure.github.io/azureml-web/docs/vs-code-snippets/snippets). Algemene code fragmenten van Azure ML zijn nu beschikbaar in uw hand bereik. Navigeer naar het paneel code fragmenten dat toegankelijk is via de werk balk of Activeer het menu met code fragmenten met Ctrl + spatie.  
  + [Sneltoetsen](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#shortcut-keys). Volledige pariteit met sneltoetsen die beschikbaar zijn in Jupyter. 
  + Geef de para meters van de cel aan. Toont gebruikers met een cel in een notitie blok is een para meter-cel en kan notitie blokken met para meters uitvoeren via [Papermill](https://github.com/nteract/papermill) op het reken proces.
  + Terminal en kernel Session Manager: gebruikers kunnen alle kernels en Terminal sessies beheren die op uw computer worden uitgevoerd.
  + Knop delen. Gebruikers kunnen nu bestanden in het notebook bestand Verkenner delen door met de rechter muisknop op het bestand te klikken en de knop delen te gebruiken.


+ **Oplossingen en verbeteringen voor oplossingen**
  + Verbeterde laad tijden voor pagina's
  + Verbeterde prestaties 
  + Verbeterde snelheid en kernel-betrouw baarheid
  + Het draai wiel is toegevoegd om de voortgang weer te geven voor alle actieve [reken activiteiten](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#status-indicators).
  + Klik met de rechter muisknop in Verkenner. Als u met de rechter muisknop op een bestand klikt, worden nu Bestands bewerkingen geopend. 

## <a name="2021-02-16"></a>2021-02-16

### <a name="azure-machine-learning-sdk-for-python-v1230"></a>Azure Machine Learning SDK voor python v-1.23.0
+ **Nieuwe functies**
  + **azureml-core**
    + [Experimentele functie] Voeg ondersteuning toe om Synapse-werk ruimte te koppelen aan AML als een gekoppelde service
    + [Experimentele functie] Voeg ondersteuning toe voor het koppelen van een Synapse Spark-pool in AML als reken kracht
    + [Experimentele functie] Voeg ondersteuning toe voor toegang op basis van identiteits gegevens. Gebruikers kunnen gegevens opslag of gegevens sets registreren zonder referenties op te geven. In dat geval wordt het AAD-token van de gebruiker of de beheerde identiteit van het Compute-doel gebruikt voor verificatie. Klik [hier](https://aka.ms/data-access) voor meer informatie.
  + **azureml-pipeline-steps**
    + [Experimentele functie] Ondersteuning voor [SynapseSparkStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.synapsesparkstep) toevoegen
  + **azureml-synapse**
    + [Experimentele functie] Voeg ondersteuning toe van Spark Magic voor het uitvoeren van een interactieve sessie in een Synapse Spark-pool.
+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-runtime**
    + In deze update hebben we Holt winters exponentiële smoothing toegevoegd aan de voor spellingen van de werkset van AutoML SDK. Op basis van een tijd reeks wordt het beste model geselecteerd door [AICc (gecorrigeerd Akaike Information criterium)](https://otexts.com/fpp3/selecting-predictors.html#selecting-predictors) en geretourneerd.
    + AutoML genereert nu twee logboek bestanden in plaats van één. De instructies van het logboek gaan naar een of de andere, afhankelijk van het proces waarin de logboek instructie is gegenereerd.
    + Verwijder onnodige voor beeld-voor spelling tijdens model training met kruis validaties. Dit kan in sommige gevallen de trainings tijd van het model verlagen, met name voor het maken van prognose modellen voor time-series.
  + **azureml-contrib-fairness**
    + Voeg een JSON-schema toe voor de dashboardDictionary-uploads.
  + **azureml-contrib-interpret**
    + het contrib-Leesmij-bestand voor het interpreteren van azureml is bijgewerkt met het pakket dat in de volgende update wordt verwijderd nadat dit is afgeschaft sinds oktober, gebruikt u in plaats daarvan het pakket azureml-interpret
  + **azureml-core**
    + Voorheen was het mogelijk een inrichtings configuratie te maken met het minimum aantal knoop punten dat kleiner is dan het maximum aantal knoop punten. Dit is nu opgelost. Als u nu probeert een inrichtings configuratie te maken met `min_nodes < max_nodes` de SDK, wordt er een gegenereerd `ComputeTargetException` .
    +  Hiermee wordt een fout opgelost in wait_for_completion in AmlCompute, waardoor de functie de controle stroom heeft geretourneerd voordat de bewerking daad werkelijk is voltooid
    + Run. Fail () is nu afgeschaft, gebruik run. tag () om Run as-bewerking te markeren als mislukt of gebruik run. Cancel () om de run as canceld te markeren.
    + Fout bericht weer geven ' omgevings naam verwachte Str, {} gevonden ' wanneer de gegeven omgevings naam geen teken reeks is.
  + **azureml-train-automl-client**
    + Er is een fout opgelost die verhindert dat er AutoML experimenten worden uitgevoerd op Azure Databricks clusters worden geannuleerd.


## <a name="2021-02-09"></a>2021-02-09

### <a name="azure-machine-learning-sdk-for-python-v1220"></a>Azure Machine Learning SDK voor python v-1.22.0
+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + Er is een fout opgelost waarbij een extra PIP-afhankelijkheid is toegevoegd aan het Conda yml-bestand voor Vision-modellen.
  + **azureml-automl-runtime**
    + Er is een fout opgelost waarbij klassieke prognose modellen (bijvoorbeeld AutoArima) trainings gegevens kunnen ontvangen waarbij rijen met toegerekende doel waarden niet aanwezig zijn. Dit heeft het gegevens contract van deze modellen geschonden. * Verschillende bugs met een vertraagd gedrag van de time-series-operator. Voorheen heeft de bewerking lag per exemplaar niet alle toegerekende rijen correct gemarkeerd en daarom wordt niet altijd de juiste vertragings waarden voor het voorval gegenereerd. Er zijn ook compatibiliteits problemen opgelost tussen de vertragings operator en de operator voor het rollen venster met het gedrag voor herhalingen. Dit heeft eerder geresulteerd in de operator voor het neerzetten van een aantal rijen uit de trainings gegevens die op een andere manier moeten worden gebruikt.
  + **azureml-core**
    + Ondersteuning voor token verificatie toevoegen per doel groep.
    + Voeg toe `process_count` aan [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) om multi-process PyTorch-taken met meerdere knoop punten te ondersteunen.
  + **azureml-pipeline-steps**
    + [CommandStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.commandstep) nu en zonder experimenteel.
    + [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig): Voeg het argument allowed_failed_count en allowed_failed_percent toe om de fout drempelwaarde op het niveau van de batch te controleren. De drempel waarde voor fouten heeft nu drie versies:
       + error_threshold: het aantal toegestane, mislukte mini-batch items; 
       + allowed_failed_count: het aantal mislukte mini batches dat is toegestaan. 
       + allowed_failed_percent-het percentage toegestane mislukte mini batches. 
       
       Een taak wordt gestopt als deze meer dan een waarde overschrijdt. error_threshold is vereist om ervoor te zorgen dat het achterwaarts compatibel is. Stel de waarde in op-1 om deze te negeren.
    + Vaste spatie verwerking in AutoMLStep-naam.
    + ScriptRunConfig wordt nu ondersteund door HyperDriveStep
  + **azureml-train-core**
    + HyperDrive-uitvoeringen die vanuit een ScriptRun worden aangeroepen, worden nu beschouwd als een onderliggende uitvoering.
    + Voeg toe `process_count` aan [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) om multi-process PyTorch-taken met meerdere knoop punten te ondersteunen.
  + **azureml-widgets**
    + Widget ParallelRunStepDetails toevoegen om de status van een ParallelRunStep te visualiseren.
    + Hiermee kunnen Hyperdrive-gebruikers een extra as weer geven in het parallel coördinaten diagram met de metrische waarde die overeenkomt met elke set van Hyper parameters voor elke onderliggende uitvoering.


 ## <a name="2021-01-31"></a>2021-01-31
### <a name="azure-machine-learning-studio-notebooks-experience-january-update"></a>Ervaring met Azure Machine Learning Studio notitie blokken (update van januari)
+ **Nieuwe functies**
  + Systeem eigen prijs verkortings editor in AzureML. Gebruikers kunnen nu afprijsingen van bestanden in AzureML Studio weer geven en bewerken.
  + De [knop uitvoeren voor scripts (. py,. R en. v)](./how-to-run-jupyter-notebooks.md#run-a-notebook-or-python-script). Gebruikers kunnen nu python-, R-en bash-script uitvoeren in AzureML
  + [Variabele Explorer](./how-to-run-jupyter-notebooks.md#explore-variables-in-the-notebook). De inhoud van variabelen en gegevens frames in een pop-updeelvenster verkennen. Gebruikers kunnen eenvoudig gegevens type, grootte en inhoud controleren.
  + [Inhouds opgave](./how-to-run-jupyter-notebooks.md#navigate-with-a-toc). Navigeer naar secties van uw notitie blok, aangegeven door de kopteksten van de prijs opgave.
  + Exporteer uw notebook als latex/HTML/py. Eenvoudig te delen notebook-bestanden maken door deze te exporteren naar LaTex, HTML of. py
  + Intellicode. ML-krachtige resultaten bieden een verbeterde slimme functie voor automatisch [aanvullen](/visualstudio/intellicode/overview).

+ **Oplossingen en verbeteringen voor oplossingen**
  + Verbeterde laad tijden voor pagina's
  + Verbeterde prestaties 
  + Verbeterde snelheid en kernel-betrouw baarheid
  

 ## <a name="2021-01-25"></a>2021-01-25

### <a name="azure-machine-learning-sdk-for-python-v1210"></a>Azure Machine Learning SDK voor python v-1.21.0
+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Help-tekst voor vaste CLI bij het gebruik van AmlCompute met UserAssigned-identiteit
  + **azureml-contrib-automl-DNN-Vision**
    + Knoppen voor implementeren en downloaden worden zichtbaar voor AutoML Vision-runs en modellen kunnen worden geïmplementeerd of gedownload, vergelijkbaar met andere AutoML-uitvoeringen. Er zijn twee nieuwe bestanden (scoring_file_v_1_0_0. py en conda_env_v_1_0_0. yml) die een script bevatten voor het uitvoeren van een deinterferentie en een yml-bestand om de Conda-omgeving opnieuw te maken. De naam van het bestand ' model. pth ' is ook gewijzigd om de extensie '. pt ' te gebruiken.
  + **azureml-core**
    + MSI-ondersteuning voor Azure-cli-ml
    + Door de gebruiker toegewezen ondersteuning voor beheerde identiteit.
    + Met deze wijziging moeten de klanten een door de gebruiker toegewezen identiteit kunnen opgeven die kan worden gebruikt voor het ophalen van de sleutel van de sleutel kluis van de klant voor versleuteling in rust.
    +  Fix row_count = 0 voor het profiel van zeer grote bestanden-herstel fout in dubbele conversie voor door komma's gescheiden waarden met een witruimte opvulling
    + Experimentele vlag verwijderen voor uitvoer gegevensset GA
    + Documentatie over het ophalen van een specifieke versie van een model bijwerken
    + Werk ruimte voor het bijwerken van toegang tot de gemengde modus toestaan in het geval van een persoonlijke koppeling
    + Oplossing voor het verwijderen van aanvullende registratie voor het uitvoeren van de functie voor het hervatten van de gegevens
    + CLI/SDK-ondersteuning toegevoegd voor het bijwerken van de toegewezen identiteit van de primaire gebruiker van de werk ruimte
  + **azureml-interpret**
    + bijgewerkt azureml-interpreteren om te interpreteren-Community 0.16.0
    + geheugen optimalisaties voor uitleg client in azureml-interprete
  + **azureml-train-automl-runtime**
    + Streaming ingeschakeld voor ADB-uitvoeringen
  + **azureml-train-core**
    + Oplossing voor het verwijderen van aanvullende registratie voor het uitvoeren van de functie voor het hervatten van de gegevens
  + **azureml-widgets**
    + Klanten mogen geen wijzigingen zien in bestaande visualisaties voor het uitvoeren van gegevens met behulp van de widget en er wordt nu ondersteuning geboden als ze optioneel gebruikmaken van voorwaardelijke Hyper parameters.
    + De widget gebruikers sessie bevat nu een gedetailleerde uitleg voor de reden dat een uitvoering zich in de wachtrij status bevindt.


 ## <a name="2021-01-11"></a>2021-01-11

### <a name="azure-machine-learning-sdk-for-python-v1200"></a>Azure Machine Learning SDK voor python v-1.20.0
+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + framework_version toegevoegd in OptimizationConfig. Het wordt gebruikt wanneer model is geregistreerd met Framework MULTI.
  + **azureml-contrib-optimalisatie**
    + framework_version toegevoegd in OptimizationConfig. Het wordt gebruikt wanneer model is geregistreerd met Framework MULTI.
  + **azureml-pipeline-steps**
    + Inleiding tot CommandStep, die de opdracht zal verwerken. Opdracht kan uitvoer bare bestanden, shell-opdrachten, scripts enzovoort bevatten.
  + **azureml-core**
    + Het maken van werk ruimten wordt nu door de gebruiker toegewezen identiteit ondersteund. De UAI-ondersteuning van SDK/CLI toevoegen
    + Er is een probleem opgelost met de service. opnieuw laden () om wijzigingen in score.py op te halen in de lokale implementatie.
    + `run.get_details()` bevat een extra veld met de naam ' submittedBy ', waarin de auteurnaam voor deze uitvoering wordt weer gegeven.
    + Bewerkings model. REGI ster-methode documentatie om te vermelden hoe model moet worden geregistreerd bij het rechtstreeks uitvoeren
    + Verwerkings probleem bij het wijzigen van de verbindings status van IOT-Server.
   

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Ervaring met Azure Machine Learning Studio notitie blokken (update van december)
+ **Nieuwe functies**
  + Zoek opdracht van de gebruikers naam. Gebruikers kunnen nu zoeken in alle bestanden die zijn opgeslagen in een werk ruimte.
  + De ondersteunings periode naast elkaar afprijsen per notebook-cel. In een notebook-cel kunnen gebruikers nu beschikken over de optie voor het weer geven van de syntaxis voor gerenderde prijs verlaging en korting naast elkaar.
  + Status balk van cel. De status balk geeft aan in welke staat een code-cel zich bevindt, of een cel is uitgevoerd en hoe lang het duurt om te worden uitgevoerd. 
   
+ **Oplossingen en verbeteringen voor oplossingen**
  + Verbeterde laad tijden voor pagina's
  + Verbeterde prestaties 
  + Verbeterde snelheid en kernel-betrouw baarheid

  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Azure Machine Learning SDK voor python v-1.19.0
+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + Experimentele ondersteuning voor test gegevens is toegevoegd aan AutoMLStep.
    + De initiële basis implementatie van de opname functie voor test sets is toegevoegd.
    + Verwijzingen naar sklearn. Externals. joblib zijn verplaatst naar de joblib.
    + een nieuw AutoML-taak type van "installatie kopie-exemplaar-segmentatie" introduceren.
  + **azureml-automl-runtime**
    + De initiële basis implementatie van de opname functie voor test sets is toegevoegd.
    + Wanneer alle teken reeksen in een tekst kolom een lengte van precies één teken hebben, werkt de TfIdf woord-gram featurizer niet omdat de Tokenizer de teken reeksen van minder dan twee tekens negeert. Met de huidige code wijziging kan AutoML deze use-case verwerken.
    + een nieuw AutoML-taak type van "installatie kopie-exemplaar-segmentatie" introduceren.
  + **azureml-contrib-automl-DNN-NLP**
    + Eerste PR voor nieuw DNN-NLP-pakket
  + **azureml-contrib-automl-DNN-Vision**
    + een nieuw AutoML-taak type van "installatie kopie-exemplaar-segmentatie" introduceren.
  + **azureml-contrib-automl-pijp lijn-stappen**
    + Dit nieuwe pakket is verantwoordelijk voor het maken van stappen die vereist zijn voor veel modellen trein/Afleidings scenario. -Het verplaatst de code van de trein/afleiding naar het pakket azureml. Train. automl. runtime, zodat toekomstige oplossingen automatisch beschikbaar zijn via software releases met de status.
  + **azureml-contrib-dataset**
    + een nieuw AutoML-taak type van "installatie kopie-exemplaar-segmentatie" introduceren.
  + **azureml-core**
    + De initiële basis implementatie van de opname functie voor test sets is toegevoegd.
    + De XREF-waarschuwingen voor documentatie in het azureml core-pakket herstellen
    + Doc-teken reeks oplossingen voor de functie opdracht ondersteuning in SDK
    + Opdracht eigenschap toevoegen aan RunConfiguration. Met deze functie kunnen gebruikers een daad werkelijke opdracht of uitvoer bare bestanden uitvoeren op de compute via de AzureML-SDK.
    + Gebruikers kunnen een leeg experiment verwijderen, gezien de ID van het experiment.
  + **azureml-dataprep**
    + Er is ondersteuning toegevoegd voor de gegevensset voor Spark die is gebouwd met scala 2,12. Hiermee wordt de bestaande 2,11-ondersteuning toegevoegd.
  + **azureml-mlflow**
    + AzureML-MLflow voegt veilige beveiligings beveiligingen in externe scripts toe om te voor komen dat verzonden uitvoeringen vroegtijdig worden beëindigd.
  + **azureml-pipeline-core**
    + Er is een fout opgelost in het instellen van een standaard pijplijn voor het pijp lijn-eind punt dat is gemaakt via de gebruikers interface
  + **azureml-pipeline-steps**
    + Experimentele ondersteuning voor test gegevens is toegevoegd aan AutoMLStep.
  + **azureml-tensorboard**
    + De XREF-waarschuwingen voor documentatie in het azureml core-pakket herstellen
  + **azureml-train-automl-client**
    + Experimentele ondersteuning voor test gegevens is toegevoegd aan AutoMLStep.
    + De initiële basis implementatie van de opname functie voor test sets is toegevoegd.
    + een nieuw AutoML-taak type van "installatie kopie-exemplaar-segmentatie" introduceren.
  + **azureml-train-automl-runtime**
    + De initiële basis implementatie van de opname functie voor test sets is toegevoegd.
    + Los de berekening van de onbewerkte uitleg voor het beste AutoML-model op als de AutoML-modellen zijn getraind met validation_size instelling.
    + Verwijzingen naar sklearn. Externals. joblib zijn verplaatst naar de joblib.
  + **azureml-train-core**
    + HyperDriveRun.get_children_sorted_by_primary_metric () moet nu sneller worden voltooid
    + Verbeterde fout afhandeling in de HyperDrive-SDK.
    +  Alle Estimator-klassen zijn afgeschaft in het voor deel van het gebruik van ScriptRunConfig voor het configureren van experimenten. Afgeschafte klassen zijn:
        + MMLBase
        + Estimator
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + Het gebruik van Nccl en Gloo is afgeschaft als geldige invoer typen voor Estimator-klassen in het voor deel van het gebruik van PyTorchConfiguration met ScriptRunConfig.
    + Het gebruik van MPI is afgeschaft als een geldig invoer type voor Estimator-klassen in het voor deel van het gebruik van MpiConfiguration met ScriptRunConfig.
    + Opdracht eigenschap toevoegen aan runconfiguration. Met deze functie kunnen gebruikers een daad werkelijke opdracht of uitvoer bare bestanden uitvoeren op de compute via de AzureML-SDK.

    +  Alle Estimator-klassen zijn afgeschaft in het voor deel van het gebruik van ScriptRunConfig voor het configureren van experimenten. Afgeschafte klassen zijn: + MMLBaseEstimator + Estimator + PyTorch + tensor flow + Chainer + SKLearn
    + Het gebruik van Nccl en Gloo is afgeschaft als een geldig type invoer voor Estimator-klassen in het voor deel van het gebruik van PyTorchConfiguration met ScriptRunConfig. 
    + Het gebruik van MPI is afgeschaft als een geldig type invoer voor Estimator-klassen in het voor deel van het gebruik van MpiConfiguration met ScriptRunConfig.

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Ervaring met Azure Machine Learning Studio notitie blokken (update van november)
+ **Nieuwe functies**
   + Systeem eigen terminal. Gebruikers hebben nu toegang tot een geïntegreerde Terminal en een Git-bewerking via de [Geïntegreerde Terminal.](./how-to-access-terminal.md)
  + Dubbele map 
  + Kostprijs berekening voor de vervolg keuzelijst compute 
  + Offline Compute-Pylance 

+ **Oplossingen en verbeteringen voor oplossingen**
  + Verbeterde laad tijden voor pagina's
  + Verbeterde prestaties 
  + Verbeterde snelheid en kernel-betrouw baarheid
  + Grote bestanden uploaden. U kunt nu bestand uploaden >95mb

## <a name="2020-11-09"></a>2020-11-09

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Azure Machine Learning SDK voor python v-1.18.0
+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    +  Verbeterde verwerking van korte tijd reeksen door opvullingen met Gaussiaans ruis toe te staan.
  + **azureml-automl-runtime**
    + ConfigException genereren als de kolom DateTime een OutOfBoundsDatetime-waarde heeft
    + Verbeterde verwerking van korte tijd reeksen door opvullingen met Gaussiaans ruis toe te staan.
    + Zorg ervoor dat elke tekst kolom gebruik kan maken van char-gram Transform met het n-gram-bereik op basis van de lengte van de teken reeksen in die tekst kolom
    + Onbewerkte functie verklaringen bieden voor de beste modus voor AutoML experimenten die worden uitgevoerd op de lokale computer van de gebruiker
  + **azureml-core**
    + Het pakket vastmaken: pyjwt om te voor komen dat de versies in de komende releases worden gebroken.
    + Bij het maken van een experiment wordt het actieve of laatste gearchiveerde experiment geretourneerd met dezelfde gegeven naam als dit experiment bestaat of een nieuw experiment.
    + Als get_experiment op naam wordt aangeroepen, wordt het actieve of laatste gearchiveerde experiment geretourneerd met de betreffende naam.
    + Gebruikers kunnen de naam van een experiment niet wijzigen terwijl het opnieuw wordt geactiveerd.
    + Verbeterd fout bericht om mogelijke oplossingen op te halen wanneer een gegevensset onjuist wordt door gegeven aan een experiment (bijvoorbeeld ScriptRunConfig). 
    + Verbeterde documentatie voor `OutputDatasetConfig.register_on_complete` om te bepalen wat er gebeurt wanneer de naam al bestaat.
    + Het opgeven van invoer-en uitvoer namen van een gegevensset die de kans op conflicten met gemeen schappelijke omgevings variabelen hebben, resulteren nu in een waarschuwing
    + De para meter is opnieuw bedoelt `grant_workspace_access` bij het registreren van gegevens opslag. Stel deze in op `True` om toegang te krijgen tot gegevens achter het virtuele netwerk vanaf machine learning Studio.
      [Meer informatie](./how-to-enable-studio-virtual-network.md)
    + De API van de gekoppelde service is verfijnd. In plaats van een resource-ID op te geven, hebben we drie afzonderlijke para meters sub_id, RG en de naam gedefinieerd in de configuratie.
    + Als u wilt dat klanten zelf problemen met token beschadiging kunnen oplossen, kunt u de synchronisatie van werk ruimte-tokens op een open bare manier inschakelen.
    + Met deze wijziging kan een lege teken reeks worden gebruikt als waarde voor een script_param
  + **azureml-train-automl-client**
    +  Verbeterde verwerking van korte tijd reeksen door opvullingen met Gaussiaans ruis toe te staan.
  + **azureml-train-automl-runtime**
    + ConfigException genereren als de kolom DateTime een OutOfBoundsDatetime-waarde heeft
    + Er is ondersteuning toegevoegd voor het leveren van onbewerkte functie verklaringen voor het beste model voor AutoML experimenten die worden uitgevoerd op de lokale computer van de gebruiker
    + Verbeterde verwerking van korte tijd reeksen door opvullingen met Gaussiaans ruis toe te staan.
  + **azureml-train-core**
    + Met deze wijziging kan een lege teken reeks worden gebruikt als waarde voor een script_param
  + **azureml-Train-restclients-Hyperdrive**
    + Leesmij is gewijzigd om meer context te bieden
  + **azureml-widgets**
    + Teken reeks ondersteuning toevoegen aan grafieken/parallelle coördinaten bibliotheek voor widget.

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>Gegevens labelen voor het segmenteren van installatie kopieën (veelhoek aantekening) (preview-versie)

Het project type van de afbeeldings exemplaar segmentatie (polygoon aantekeningen) in gegevens labeling is nu beschikbaar, zodat gebruikers kunnen tekenen en aantekeningen maken met veelhoeken rond de contour van de objecten in de afbeeldingen. Gebruikers kunnen een klasse en een veelhoek toewijzen aan elk object dat van belang is binnen een afbeelding.

Meer informatie over de [segmentatie van afbeeldings instanties](how-to-label-images.md).



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Azure Machine Learning SDK voor python v-1.17.0
+ **nieuwe voor beelden**
  + Er is een nieuwe, door de Community aangedreven opslag plaats van voor beelden beschikbaar op https://github.com/Azure/azureml-examples
+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + Er is een probleem opgelost waarbij get_output een XGBoostError kan veroorzaken.
  + **azureml-automl-runtime**
    + Tijd/Calendar-functies die door AutoML worden gemaakt, hebben nu het voor voegsel.
    + Er is een IndexError opgelost die optreedt tijdens de training van StackEnsemble voor classificatie gegevens sets met een groot aantal klassen en subsampling is ingeschakeld.
    + Er is een probleem opgelost waarbij VotingRegressor-voor spellingen mogelijk onnauwkeurig zijn na het aanpassen van het model.
  + **azureml-core**
    + Aanvullende details toegevoegd over de relatie tussen de AKS-implementatie configuratie en Azure Kubernetes service-concepten.
    + Ondersteuning voor de client labels van de omgeving. De gebruiker kan een label omgeving geven en ernaar verwijzen.
  + **azureml-dataprep**
    + Er is een beter fout bericht bij het gebruik van momenteel niet-ondersteunde Spark met scala 2,12.
  + **azureml-explain-model**
    + Het pakket voor de azureml-uitleg-model is officieel afgeschaft
  + **azureml-mlflow**
    + Er is een fout opgelost in mlflow. projects. kan niet worden uitgevoerd op de azureml-back-end, waarbij de voltooiings status niet correct is verwerkt.
  + **azureml-pipeline-core**
    + Voeg ondersteuning toe voor het maken, weer geven en ophalen van een pipeline-eind punt op basis van een pijplijn.
    +  De documentatie van PipelineData.as_dataset is verbeterd met een ongeldig gebruik voor beeld: als u PipelineData.as_dataset onjuist gebruikt, wordt er nu een ValueException gegenereerd
    + De HyperDriveStep pipelines-notebook is gewijzigd om het beste model binnen een PipelineStep te registreren, direct nadat de HyperDriveStep is uitgevoerd.
  + **azureml-pipeline-steps**
    + De HyperDriveStep pipelines-notebook is gewijzigd om het beste model binnen een PipelineStep te registreren, direct nadat de HyperDriveStep is uitgevoerd.
  + **azureml-train-automl-client**
    + Er is een probleem opgelost waarbij get_output een XGBoostError kan veroorzaken.

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Ervaring met Azure Machine Learning Studio notitie blokken (update van oktober)
+ **Nieuwe functies**
  + [Volledige ondersteuning voor virtueel netwerk](./how-to-enable-studio-virtual-network.md)
  + [Focus modus](./how-to-run-jupyter-notebooks.md#focus-mode)
  + Notitie blokken opslaan CTRL-S
  + Regel nummers

+ **Oplossingen en verbeteringen voor oplossingen**
  + Verbetering van de snelheid en kernel-betrouw baarheid
  + UI-updates voor Jupyter-widget

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Azure Machine Learning SDK voor python v-1.16.0
+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + AKSWebservice en AKSEndpoints bieden nu ondersteuning voor pod CPU-en geheugen resource limieten. Deze optionele limieten kunnen worden gebruikt door instellingen `--cpu-cores-limit` en `--memory-gb-limit` vlaggen in TOEPAS bare cli-aanroepen
  + **azureml-core**
    + Primaire versies van de directe afhankelijkheden van azureml-core vastmaken
    + AKSWebservice en AKSEndpoints bieden nu ondersteuning voor pod CPU-en geheugen resource limieten. Meer informatie over [Kubernetes-resources en-limieten](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)
    + Run.log_table bijgewerkt zodat afzonderlijke rijen kunnen worden geregistreerd.
    + Statische methode toegevoegd `Run.get(workspace, run_id)` om een uitvoering alleen op te halen met behulp van een werk ruimte 
    + Methode `Workspace.get_run(run_id)` voor het toevoegen van een exemplaar voor het ophalen van een uitvoering in de werk ruimte
    + Hiermee wordt de opdracht eigenschap in een uitvoerings configuratie geïntroduceerd, waarmee gebruikers opdracht kunnen verzenden in plaats van script & argumenten.
  + **azureml-interpret**
    + gedrag van de vlag voor client is_raw met vaste uitleg in azureml-interpret
  + **azureml-SDK**
    + `azureml-sdk` officiële ondersteuning voor python 3,8.
  + **azureml-train-core**
    + Tensor Flow 2,3-gecuratore omgeving toevoegen
    + Hiermee wordt de opdracht eigenschap in een uitvoerings configuratie geïntroduceerd, waarmee gebruikers opdracht kunnen verzenden in plaats van script & argumenten.
  + **azureml-widgets**
    + Opnieuw ontworpen interface voor widget script uitvoering.


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Azure Machine Learning SDK voor python v-1.15.0
+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-contrib-interpret**
    + KALK uitleg is verplaatst van azureml-contrib-interpreteren op interpreteren-Community-pakket en afbeeldings uitleg uit het pakket azureml-contrib-interpreteren is verwijderd
    + het visualisatie dashboard is verwijderd uit het contrib-pakket, de uitleg-client is verplaatst naar het pakket voor de azureml-interpreter en afgeschaft in het pakket en de notebooks die zijn bijgewerkt met een verbeterde API
    + pypi-pakket beschrijvingen voor azureml interpreteren, azureml-uitleggen-model, azureml-contrib-interpreteren en azureml-tensorboard
  + **azureml-contrib-notebook**
    + Maak een nbcovert afhankelijkheid van < 6 zodat Papermill 1. x blijft werken.
  + **azureml-core**
    + Er zijn para meters toegevoegd aan de constructor TensorflowConfiguration en MpiConfiguration om een meer gestroomlijnde initialisatie van de klassen kenmerken mogelijk te maken zonder dat de gebruiker elk afzonderlijk kenmerk hoeft in te stellen. Er is een PyTorchConfiguration-klasse toegevoegd voor het configureren van gedistribueerde PyTorch-taken in ScriptRunConfig.
    + Maak de versie van Azure-beheer-resource vast om de verificatie fout op te lossen.
    + Ondersteuning voor Triton zonder code implementeren
    + uitvoer mappen die zijn opgegeven in Run.start_logging () worden nu bijgehouden wanneer u uitvoeren in interactieve scenario's gebruikt. De bijgehouden bestanden worden weer gegeven op ML Studio bij het aanroepen van run. complete ()
    + Bestands codering kan nu worden opgegeven tijdens het maken van `Dataset.Tabular.from_delimited_files` de gegevensset met en `Dataset.Tabular.from_json_lines_files` door het argument door te geven `encoding` . De ondersteunde code ringen zijn ' utf8 ', ' iso88591 ', ' Latin1 ', ' ASCII ', UTF16 ', ' utf32 ', ' utf8bom ' en ' windows1252 '.
    + Fout oplossing wanneer het omgevings object niet wordt door gegeven aan de ScriptRunConfig-constructor.
    + Run. Cancel () is bijgewerkt, zodat een lokale uitvoering van een andere computer kan worden geannuleerd.
  + **azureml-dataprep**
    +  Problemen met time-out bij het koppelen van vaste gegevensset.
  + **azureml-explain-model**
    + pypi-pakket beschrijvingen voor azureml interpreteren, azureml-uitleggen-model, azureml-contrib-interpreteren en azureml-tensorboard
  + **azureml-interpret**
    + het visualisatie dashboard is verwijderd uit het contrib-pakket, de uitleg-client is verplaatst naar het pakket voor de azureml-interpreter en afgeschaft in het pakket en de notebooks die zijn bijgewerkt met een verbeterde API
    + pakket voor azureml-interpretes bijgewerkt zodat het afhankelijk is van interpreteren-Community 0.15.0
    + pypi-pakket beschrijvingen voor azureml interpreteren, azureml-uitleggen-model, azureml-contrib-interpreteren en azureml-tensorboard
  + **azureml-pipeline-core**
    +  Probleem met de vaste pijp lijn waarbij `OutputFileDatasetConfig` het systeem niet meer reageert wanneer het `register_on_complete` wordt aangeroepen met de `name` para meter die is ingesteld op een vooraf bestaande gegevenssetnaam.
  + **azureml-pipeline-steps**
    + Verouderde databricks-notebooks verwijderd.
  + **azureml-tensorboard**
    + pypi-pakket beschrijvingen voor azureml interpreteren, azureml-uitleggen-model, azureml-contrib-interpreteren en azureml-tensorboard
  + **azureml-train-automl-runtime**
    + het visualisatie dashboard is verwijderd uit het contrib-pakket, de uitleg-client is verplaatst naar het pakket voor de azureml-interpreter en afgeschaft in het pakket en de notebooks die zijn bijgewerkt met een verbeterde API
  + **azureml-widgets**
    + het visualisatie dashboard is verwijderd uit het contrib-pakket, de uitleg-client is verplaatst naar het pakket voor de azureml-interpreter en afgeschaft in het pakket en de notebooks die zijn bijgewerkt met een verbeterde API

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Azure Machine Learning SDK voor python v-1.14.0
+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Raster profilering verwijderd uit de SDK en wordt niet meer ondersteund.
  + **azureml-accel-models**
    + het pakket voor azureml-accel.-modellen ondersteunt nu tensor Flow 2. x
  + **azureml-automl-core**
    + Fout afhandeling toegevoegd in get_output voor gevallen waarin lokale versies van Pandas/sklearn niet overeenkomen met de toepassingen die tijdens de training worden gebruikt
  + **azureml-automl-runtime**
    + Er is een fout opgelost waarbij AutoArima-iteraties mislukken met een PredictionException en het bericht: ' er is een stille fout opgetreden tijdens de voor spelling '.
  + **azureml-cli-algemeen**
    + Raster profilering verwijderd uit de SDK en wordt niet meer ondersteund.
  + **azureml-contrib-server**
    + Update beschrijving van de pagina pakket voor pypi overzicht.
  + **azureml-core**
    + Raster profilering verwijderd uit de SDK en wordt niet meer ondersteund.
    + Verminder het aantal fout berichten wanneer het ophalen van werk ruimten mislukt.
    + Geen waarschuwing weer geven als meta gegevens niet worden opgehaald
    + Nieuwe Kusto-stap en Kusto Compute-doel.
    + Document bijwerken voor SKU-para meter. Verwijder de SKU in de werk ruimte-update functionaliteit in CLI en SDK.
    + Update beschrijving van de pagina pakket voor pypi overzicht.
    + Bijgewerkte documentatie voor AzureML-omgevingen.
    + Services Managed resources-instellingen voor de AML-werk ruimte in SDK beschikbaar maken.
  + **azureml-dataprep**
    + Machtiging voor uitvoeren inschakelen voor bestanden voor gegevensset-koppeling.
  + **azureml-mlflow**
    + Bijgewerkte voor beelden van AzureML MLflow-documentatie en-notebooks 
    + Nieuwe ondersteuning voor MLflow-projecten met AzureML-back-end
    + Ondersteuning voor MLflow-model register
    + Azure RBAC-ondersteuning is toegevoegd voor AzureML-MLflow bewerkingen 
    
  + **azureml-pipeline-core**
    + De documentatie van de PipelineOutputFileDataset.parse_ *-methoden is verbeterd.
    + Nieuwe Kusto-stap en Kusto Compute-doel.
    + De gegeven Swaggerurl-eigenschap voor pijplijn entiteit via die gebruiker kan de schema definitie voor het gepubliceerde pipeline-eind punt zien.
  + **azureml-pipeline-steps**
    + Nieuwe Kusto-stap en Kusto Compute-doel.
  + **azureml-telemetry**
    + Update beschrijving van de pagina pakket voor pypi overzicht.
  + **azureml-trainen**
    + Update beschrijving van de pagina pakket voor pypi overzicht.
  + **azureml-train-automl-client**
    + Fout afhandeling toegevoegd in get_output voor gevallen waarin lokale versies van Pandas/sklearn niet overeenkomen met de toepassingen die tijdens de training worden gebruikt
  + **azureml-train-core**
    + Update beschrijving van de pagina pakket voor pypi overzicht.
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK voor python v-1.13.0
+ **Preview-functies**
  + **azureml-core** Met de nieuwe functie voor uitvoer gegevens sets kunt u terugschrijven naar de Cloud opslag met inbegrip van BLOB, ADLS gen 1, ADLS gen 2 en file share. U kunt configureren waar gegevens worden uitgevoerd, hoe gegevens moeten worden uitgevoerd (via koppelen of uploaden), of de uitvoer gegevens moeten worden geregistreerd voor toekomstig hergebruik en delen en tussenliggende gegevens tussen pijplijn stappen naadloos kunnen worden gepasseerd. Dit maakt reproduceer baarheid, delen, voor komt het dupliceren van gegevens en resulteert in rendabele efficiëntie en productiviteits toename. [Meer informatie over hoe u deze kunt gebruiken](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)
    
+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + Er is validated_ {platform} _requirements.txt bestand toegevoegd om alle PIP-afhankelijkheden voor AutoML vast te maken.
    + Deze release ondersteunt modellen van meer dan 4 GB.
    + Geüpgraded AutoML-afhankelijkheden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-automl-runtime**
    + Stel horovod in op text DNN om altijd FP16-compressie te gebruiken.
    + Deze release ondersteunt modellen van meer dan 4 GB.
    + Er is een probleem opgelost waarbij AutoML mislukt met ImportError: kan de naam niet importeren `RollingOriginValidator` .
    + Geüpgraded AutoML-afhankelijkheden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-contrib-automl-DNN-Forecasting**
    + Geüpgraded AutoML-afhankelijkheden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-contrib-fairness**
    + Geef een korte beschrijving op voor azureml-contrib-verdeling.
  + **azureml-contrib-pipeline-steps**
    + Er is een bericht toegevoegd dat dit pakket wordt afgeschaft en de gebruiker moet in plaats daarvan gebruikmaken van de stappen voor azureml-pipeline.
  + **azureml-core**
    + De lijst met opdracht sleutels voor de werk ruimte is toegevoegd.
    + De para meter Tags toevoegen in de werk ruimte SDK en CLI.
    + Er is een fout opgelost waarbij het verzenden van een onderliggende run with dataset mislukt als gevolg van `TypeError: can't pickle _thread.RLock objects` .
    + Page_count standaard/documentatie voor model lijst () toevoegen.
    + Wijzig de CLI&SDK om de adbworkspace-para meter in te voeren en de werk ruimte toe te voegen ADB koppelen/ontkoppelen.
    + Los de fout op in DataSet. Update waardoor de nieuwste gegevensset-versie wordt bijgewerkt, niet de versie van de gegevensset-update werd aangeroepen. 
    + Los een fout op in Dataset.get_by_name waarmee de labels voor de nieuwste gegevensset-versie worden weer gegeven, zelfs wanneer een specifieke oudere versie is opgehaald.
  + **azureml-interpret**
    + Er zijn waarschijnlijke uitvoer waarden toegevoegd aan Shap Score-uitleg in azureml-Interpreting op basis van shap_values_output para meter van de oorspronkelijke uitleger.
  + **azureml-pipeline-core**
    + Verbeterde `PipelineOutputAbstractDataset.register` documentatie.
  + **azureml-train-automl-client**
    + Geüpgraded AutoML-afhankelijkheden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-train-automl-runtime**
    + Geüpgraded AutoML-afhankelijkheden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-train-core**
    + Gebruikers moeten nu een geldig hyperparameter_sampling ARG opgeven bij het maken van een HyperDriveConfig. Daarnaast is de documentatie voor HyperDriveRunConfig bewerkt om gebruikers te informeren over de afschaffing van HyperDriveRunConfig.
    + Herstellen van PyTorch-standaard versie naar 1,4.
    + PyTorch 1,6 & tensor Flow 2,2-installatie kopieën en een gecuratorte omgeving toevoegen.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Ervaring met Azure Machine Learning Studio notitie blokken (update voor augustus)
+ **Nieuwe functies**
  + Nieuwe landings pagina voor aan de slag 
  
+ **Preview-functies**
    + Functie verzamelen in notitie blokken. Met de functie [verzamelen](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) kunnen gebruikers nu eenvoudig notitie blokken opruimen met, verzamelen maakt gebruik van een automatische afhankelijkheids analyse van uw notitie blok, zodat de essentiële code wordt bewaard, maar alle irrelevante delen worden verwijderd.

+ **Oplossingen en verbeteringen voor oplossingen**
  + Verbetering van snelheid en betrouw baarheid
  + Fouten in de donkere modus opgelost
  + Fouten van de uitvoer schuiven vast
  + Met de voor beeld-zoek opdracht zoekt u nu alle inhoud van alle bestanden in de Azure Machine Learning voorbeeld notitieblokken opslag plaats
  + De R-cellen van meerdere regels kunnen nu worden uitgevoerd
  + "Ik vertrouw inhoud van dit bestand" wordt nu na de eerste keer automatisch gecontroleerd
  + Verbeterd dialoog venster conflict oplossing, met nieuwe optie een kopie maken
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK voor python v-1.12.0

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Voeg image_name-en image_label-para meters toe aan model. pakket () om de naam van de gemaakte pakket installatie kopie te wijzigen.
  + **azureml-automl-core**
    + AutoML genereert een nieuwe fout code van dataprep wanneer inhoud wordt gewijzigd tijdens het lezen.
  + **azureml-automl-runtime**
    + Er zijn waarschuwingen toegevoegd voor de gebruiker wanneer gegevens ontbrekende waarden bevatten, maar parametrisatie is uitgeschakeld.
    + Fouten met vaste onderliggende uitvoeringen wanneer gegevens Nan en parametrisatie zijn uitgeschakeld.
    + AutoML genereert een nieuwe fout code van dataprep wanneer inhoud wordt gewijzigd tijdens het lezen.
    + Bijgewerkte normalisatie voor het voors pellen van metrische gegevens door middel van korrels.
    + Verbeterde berekening van de prognose quantiles wanneer lookback-functies zijn uitgeschakeld.
    + Vaste Boole-spreidings matrix verwerking bij het berekenen van uitleg na AutoML.
  + **azureml-core**
    + Een nieuwe methode `run.get_detailed_status()` toont nu de gedetailleerde uitleg van de huidige uitvoerings status. Er wordt momenteel alleen uitleg voor de `Queued` status weer gegeven.
    + Voeg image_name-en image_label-para meters toe aan model. pakket () om de naam van de gemaakte pakket installatie kopie te wijzigen.
    + Nieuwe methode `set_pip_requirements()` om de volledige PIP-sectie in [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies) één keer in te stellen.
    + Registratie van referenties-minder ADLS Gen2 gegevens opslag inschakelen.
    + Er is een verbeterd fout bericht weer gegeven bij het downloaden of koppelen van een onjuist type gegevensset.
    + Voor beeld van gegevensset filter voor Time Series bijwerken met meer voor beelden van partition_timestamp die optimalisatie van filters bieden.
    + Wijzig de SDK en CLI zodanig dat subscriptionId, resourceGroup, workspacenaam, peConnectionName als para meters in plaats van ArmResourceId worden geaccepteerd bij het verwijderen van een particuliere endpoint-verbinding.
    + Experimentele decorator toont klassen naam voor eenvoudiger identificatie.
    + Beschrijvingen voor de assets in modellen worden niet langer automatisch gegenereerd op basis van een uitvoering.
  + **azureml-datadrift**
    + Markeer create_from_model-API in DataDriftDetector als afgeschaft moet worden.
  + **azureml-dataprep**
    + Er is een verbeterd fout bericht weer gegeven bij het downloaden of koppelen van een onjuist type gegevensset.
  + **azureml-pipeline-core**
    + Er is een fout opgelost bij het deserialiseren van de pijplijn grafiek die geregistreerde gegevens sets bevat.
  + **azureml-pipeline-steps**
    + RScriptStep ondersteunt RSection van azureml. core. Environment.
    + De para meter passthru_automl_config is uit de `AutoMLStep` open bare API verwijderd en geconverteerd naar een alleen-interne para meter.
  + **azureml-train-automl-client**
    + Verwijderde lokale asynchrone, beheerde omgeving wordt uitgevoerd vanuit AutoML. Alle lokale uitvoeringen worden uitgevoerd in de omgeving waarin de uitvoering is gestart.
    + Problemen met vaste moment opnamen bij het verzenden van AutoML-uitvoeringen zonder door de gebruiker verstrekte scripts.
    + Fouten met vaste onderliggende uitvoeringen wanneer gegevens Nan en parametrisatie zijn uitgeschakeld.
  + **azureml-train-automl-runtime**
    + AutoML genereert een nieuwe fout code van dataprep wanneer inhoud wordt gewijzigd tijdens het lezen.
    + Problemen met vaste moment opnamen bij het verzenden van AutoML-uitvoeringen zonder door de gebruiker verstrekte scripts.
    + Fouten met vaste onderliggende uitvoeringen wanneer gegevens Nan en parametrisatie zijn uitgeschakeld.
  + **azureml-train-core**
    + Er is ondersteuning toegevoegd voor het opgeven van PIP-opties (bijvoorbeeld--extra-index-URL) in het PIP-vereisten bestand dat is door gegeven aan een [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator) through- `pip_requirements_file` para meter.


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK voor python v-1.11.0

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Model raamwerk en model Framework herstellen niet door gegeven in het object run in het CLI-model registratie traject
    + De CLI-amlcompute identiteit weer geven opdracht voor het weer geven van de Tenant-ID en Principal-ID 
  + **azureml-train-automl-client**
    + Get_best_child () is toegevoegd aan AutoMLRun voor het ophalen van de best onderliggende run voor een AutoML-uitvoering zonder het bijbehorende model te downloaden.
    + Er is een ModelProxy-object toegevoegd waarmee voors pellen of Forecasting kan worden uitgevoerd op een externe trainings omgeving zonder het model lokaal te downloaden.
    + Niet-verwerkte uitzonde ringen in AutoML verwijzen nu naar een bekende problemen met de HTTP-pagina, waar meer informatie over de fouten kan worden gevonden.
  + **azureml-core**
    + Model namen kunnen 255 tekens lang zijn.
    + Environment.get_image_details () retour object type is gewijzigd. `DockerImageDetails` de klasse is vervangen `dict` , de details van de afbeelding zijn beschikbaar via de nieuwe klasse-eigenschappen. Wijzigingen zijn achterwaarts compatibel.
    + Bug corrigeren voor Environment.from_pip_requirements () om de structuur van afhankelijkheden op te slaan
    + Er is een fout opgelost waarbij log_list mislukt als er een int en double in dezelfde lijst zijn opgenomen.
    + Wanneer u een persoonlijke koppeling inschakelt voor een bestaande werk ruimte, moet u er rekening mee houden dat als er reken doelen zijn gekoppeld aan de werk ruimte, deze doelen niet werken als ze zich niet achter hetzelfde virtuele netwerk bevinden als het persoonlijke eind punt van de werk ruimte.
    + `as_named_input`Optioneel gemaakt wanneer gegevens sets worden gebruikt in experimenten en `as_mount` worden toegevoegd en `as_download` aan `FileDataset` . De naam van de invoer wordt automatisch gegenereerd als `as_mount` of `as_download` is aangeroepen.
  + **azureml-automl-core**
    + Niet-verwerkte uitzonde ringen in AutoML verwijzen nu naar een bekende problemen met de HTTP-pagina, waar meer informatie over de fouten kan worden gevonden.
    + Get_best_child () is toegevoegd aan AutoMLRun voor het ophalen van de best onderliggende run voor een AutoML-uitvoering zonder het bijbehorende model te downloaden.
    + Er is een ModelProxy-object toegevoegd waarmee voors pellen of Forecasting kan worden uitgevoerd op een externe trainings omgeving zonder het model lokaal te downloaden.
  + **azureml-pipeline-steps**
    + Toegevoegd `enable_default_model_output` en `enable_default_metrics_output` gemarkeerd aan `AutoMLStep` . Deze vlaggen kunnen worden gebruikt om de standaard uitvoer in-of uit te scha kelen.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK voor python v-1.10.0

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + Wanneer u AutoML gebruikt en er een pad wordt door gegeven aan het AutoMLConfig-object en dit nog niet bestaat, wordt dit automatisch gemaakt.
    + Gebruikers kunnen nu een tijd reeks frequentie voor prognose taken opgeven met behulp van de `freq` para meter.
  + **azureml-automl-runtime**
    + Wanneer u AutoML gebruikt en er een pad wordt door gegeven aan het AutoMLConfig-object en dit nog niet bestaat, wordt dit automatisch gemaakt.
    + Gebruikers kunnen nu een tijd reeks frequentie voor prognose taken opgeven met behulp van de `freq` para meter.
    + AutoML-prognoses bieden nu ondersteuning voor Rolling evaluatie, wat van toepassing is op de use-case dat de lengte van een test of validatieset langer is dan de invoer horizon, en bekende y_pred waarde wordt gebruikt als prognose context.
  + **azureml-core**
    + Er worden waarschuwings berichten afgedrukt als er geen bestanden zijn gedownload uit het gegevens archief in een uitvoering.
    + Documentatie toegevoegd voor voor `skip_validation` `Datastore.register_azure_sql_database method` .
    + Gebruikers moeten een upgrade uitvoeren naar SDK v 1.10.0 of hoger om een automatisch goedgekeurd privé-eind punt te maken. Dit geldt ook voor de notitieblok resource die zich achter het VNet bevindt.
    + Maak NotebookInfo beschikbaar in het antwoord van de werk ruimte ophalen.
    + Wijzigingen in de weer gave van Compute-doelen en het ophalen van het reken doel slagen op een externe uitvoering. SDK-functies voor het ophalen van Compute target-en List-werk ruimte-reken doelen werken nu op externe uitvoeringen.
    + Voeg afschaffing-berichten toe aan de klassen beschrijvingen voor de klassen azureml. core. image.
    + Uitzonde ring genereren en de werk ruimte en afhankelijke resources opschonen als het maken van een persoonlijk eind punt van de werk ruimte mislukt
    + Werk ruimte-SKU bijwerken in de werk ruimte-update methode.
  + **azureml-datadrift**
    + Werk de matplotlib-versie bij van 3.0.2 naar 3.2.1 ter ondersteuning van python 3,8.
  + **azureml-dataprep**
    + Er is ondersteuning toegevoegd voor web URL-gegevens bronnen met `Range` of- `Head` aanvragen. 
    + Verbeterde stabiliteit voor het koppelen en downloaden van bestand gegevensset.
  + **azureml-train-automl-client**
    + Opgeloste problemen met betrekking tot het verwijderen van `RequirementParseError` uit installatie-instellingen.
    + Docker gebruiken in plaats van Conda voor lokale uitvoeringen die worden verzonden met behulp van "compute_target = ' local '"
    + De herhalings duur die wordt afgedrukt op de-console is gecorrigeerd. Voorheen werd de iteratie duur soms afgedrukt als de eind tijd van de uitvoering minus de aanmaak tijd van de uitvoering. Het is gecorrigeerd tot een eind tijd van gelijk aan uitvoering minus de start tijd van de uitvoering.
    + Wanneer u AutoML gebruikt en er een pad wordt door gegeven aan het AutoMLConfig-object en dit nog niet bestaat, wordt dit automatisch gemaakt.
    + Gebruikers kunnen nu een tijd reeks frequentie voor prognose taken opgeven met behulp van de `freq` para meter.
  + **azureml-train-automl-runtime**
    + Verbeterde console-uitvoer wanneer de aanbevolen model uitleg mislukt.
    + De hernoemde invoer parameter op ' blocked_models ' om een gevoelige term te verwijderen.
      + De hernoemde invoer parameter op ' allowed_models ' om een gevoelige term te verwijderen.
    + Gebruikers kunnen nu een tijd reeks frequentie voor prognose taken opgeven met behulp van de `freq` para meter.

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK voor python v-1.9.0

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + Get_model_path () is vervangen door de AZUREML_MODEL_DIR omgevings variabele in het automatisch gegenereerde Score script van AutoML. Ook is er telemetrie toegevoegd om fouten tijdens init () bij te houden.
    + De mogelijkheid om op te geven `enable_cache` als onderdeel van AutoMLConfig is verwijderd
    + Er is een fout opgelost waarbij uitvoeringen mislukken met service fouten tijdens specifieke prognose uitvoeringen
    + Verbeterde fout afhandeling rond specifieke modellen tijdens `get_output`
    + Vaste oproep naar fitted_model. fit (X, y) voor classificatie met y-transformator
    + Ingeschakelde aangepaste voorwaartse doorvoer overgang voor prognose taken
    + Er wordt een nieuwe ForecastingParameters-klasse gebruikt in plaats van de voor spellingen van para meters in een dict-indeling
    + Verbeterde automatische detectie van doel vertraging
    + Er is een beperkte Beschik baarheid van multi-GPU gedistribueerde parametrisatie toegevoegd met BERT
  + **azureml-automl-runtime**
    + Prophet maakt nu gebruik van additieve seizoen lering in plaats van multiplicative.
    + Het probleem is opgelost wanneer er korte korrels zijn, waardoor de frequenties verschillen van de lange korrels.
  + **azureml-contrib-automl-DNN-Vision**
    + Systeem/GPU-statistieken verzamelen en gemiddelden van Logboeken voor training en scores
  + **azureml-contrib-Mir**
    + Er is ondersteuning toegevoegd voor de vlag Enable-app-Insights in ManagedInferencing
  + **azureml-core**
    + Een validate-para meter voor deze Api's door te zorgen dat de validatie wordt overgeslagen als de gegevens bron niet toegankelijk is vanaf de huidige compute.
      + TabularDataset.time_before (end_time, include_boundary = True, validate = True)
      + TabularDataset.time_after (start_time, include_boundary = True, validate = True)
      + TabularDataset.time_recent (time_delta, include_boundary = True, validate = True)
      + TabularDataset.time_between (start_time, end_time, include_boundary = True, validate = True)
    + Ondersteuning voor Framework-filtering voor model lijst is toegevoegd en NCD AutoML-voor beeld is toegevoegd aan het notitie blok
    + Voor Datastore.register_azure_blob_container en Datastore.register_azure_file_share (alleen opties die een SAS-token ondersteunen) hebben we de doc-teken reeksen voor het `sas_token` veld bijgewerkt met de minimale machtigingen vereisten voor normale Lees-en schrijf scenario's.
    + _With_auth param uit ws.get_mlflow_tracking_uri ()
  + **azureml-mlflow**
    + Voeg ondersteuning toe voor het implementeren van lokale file://-modellen met AzureML-MLflow
    + _With_auth param uit ws.get_mlflow_tracking_uri ()
  + **azureml-opendatasets**
    + Recent gepubliceerde Covid-19 tracking-gegevens sets zijn nu beschikbaar met de SDK
  + **azureml-pipeline-core**
    + Waarschuwing voor afmelden als ' azureml-defaults ' niet is opgenomen als onderdeel van PIP-afhankelijkheid
    + De weer gave van notities verbeteren.
    + Er is ondersteuning toegevoegd voor geciteerde regel einden bij het parseren van bestanden met scheidings tekens in PipelineOutputFileDataset.
    + De klasse PipelineDataset is afgeschaft. Voor meer informatie raadpleegt u https://aka.ms/dataset-deprecation. Zie voor meer informatie over het gebruik van dataset met pijp lijn https://aka.ms/pipeline-with-dataset .
  + **azureml-pipeline-steps**
    + Doc-updates voor azureml-pijp lijn-stappen.
    +  Er is ondersteuning toegevoegd in ParallelRunConfig `load_yaml()` voor gebruikers om in line omgevingen te definiëren met de rest van de configuratie of in een afzonderlijk bestand
  + **azureml-Train-automl-client**.
    + De mogelijkheid om op te geven `enable_cache` als onderdeel van AutoMLConfig is verwijderd
  + **azureml-train-automl-runtime**
    + Er is een beperkte Beschik baarheid van multi-GPU gedistribueerde parametrisatie toegevoegd met BERT.
    + Fout afhandeling toegevoegd voor incompatibele pakketten in op ADB gebaseerde automatische machine learning uitvoeringen.
  + **azureml-widgets**
    + Doc-updates voor objecten van azureml.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK voor python v-1.8.0
  
  + **Preview-functies**
    + **azureml-contrib-verdeling** Het `azureml-contrib-fairness` pakket biedt een integratie tussen de open-source verdeling-evaluatie en de onbillijkheid voor het beperken van het pakket [Fairlearn](https://fairlearn.github.io) en Azure machine learning Studio. Met name kunnen met het pakket verdeling-evaluatie dashboards worden geüpload als onderdeel van een AzureML-uitvoering en worden weer gegeven in Azure Machine Learning Studio

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Ondersteuning bij het ophalen van logboeken van init-container.
    + Nieuwe CLI-opdrachten toegevoegd voor het beheren van ComputeInstance
  + **azureml-automl-core**
    + Gebruikers kunnen nu stack-ensemble-herhaling voor tijdreeks taken inschakelen met een waarschuwing dat het mogelijk is overfit.
    + Er is een nieuw type gebruikers uitzondering toegevoegd dat optreedt als er is geknoeid met de inhoud van het cache-archief
  + **azureml-automl-runtime**
    + Het opruimen van klasse-verdelingen wordt niet meer ingeschakeld als de gebruiker parametrisatie uitschakelt.  
  + **azureml-contrib-ITP**
    + Reken type van CmAk wordt ondersteund. U kunt uw eigen AKS-cluster toevoegen aan de werk ruimte voor trainings taken.
  + **azureml-contrib-notebook**
    + Doc-verbeteringen in het contrib-notebook pakket.
  + **azureml-contrib-pipeline-steps**
    + Doc-verbeteringen in het pakket voor azureml-contrib--pipeline-Steps.
  + **azureml-core**
    + Set_connection toevoegen, get_connection, list_connections, delete_connection functies voor de klant voor het uitvoeren van de werk ruimte verbindings resource
    + Documentatie-updates voor het pakket azureml-coore/azureml. exceptions.
    + Documentatie-updates voor het pakket met de azureml-kern.
    + Doc wordt bijgewerkt naar de ComputeInstance-klasse.
    + Doc-verbeteringen in azureml-core/azureml. core. Compute-pakket.
    + Document verbeteringen voor aan de webservice gerelateerde klassen in azureml-core.
    + Ondersteuning voor door de gebruiker geselecteerde gegevens opslag voor het opslaan van profileren
    + De eigenschap Expand en page_count voor de model lijst-API is toegevoegd
    + Er is een probleem opgelost waarbij het verwijderen van de eigenschap overwrite de verzonden uitvoering mislukt met een fout bij het deserialiseren.
    + Vaste inconsistente mapstructuur bij het downloaden of koppelen van een FileDataset die verwijst naar één bestand.
    + Het laden van een gegevensset met Parquet-bestanden to_spark_dataframe is nu sneller en ondersteunt alle Parquet-en Spark SQL-gegevens typen.
    + Ondersteuning bij het ophalen van logboeken van init-container.
    + AutoML-uitvoeringen zijn nu gemarkeerd als onderliggend uitvoeren van de stap parallel uitvoeren.
  + **azureml-datadrift**
    + Doc-verbeteringen in het contrib-notebook pakket.
  + **azureml-dataprep**
    + Het laden van een gegevensset met Parquet-bestanden to_spark_dataframe is nu sneller en ondersteunt alle Parquet-en Spark SQL-gegevens typen.
    + Betere geheugen verwerking voor OutOfMemory-probleem voor to_pandas_dataframe.
  + **azureml-interpret**
    + Geüpgraded: interpreteren voor het gebruik van interprete-Community-versie 0,12. *
  + **azureml-mlflow**
    + Document verbeteringen voor azureml-mlflow.
    + Voegt ondersteuning toe voor het AML-model register met MLFlow.
  + **azureml-opendatasets**
    + Ondersteuning toegevoegd voor python 3,8
  + **azureml-pipeline-core**
    + `PipelineDataset`De documentatie is bijgewerkt om er een interne klasse van te maken.
    + ParallelRunStep-updates om meerdere waarden voor één argument te accepteren, bijvoorbeeld: '--group_column_names ', ' Kol1 ', ' col2 ', ' Col3 '
    + De passthru_automl_config vereiste voor het gebruik van tussenliggende gegevens met AutoMLStep in pijp lijnen is verwijderd.
  + **azureml-pipeline-steps**
    + Doc-verbeteringen in het pakket met azureml-pipeline-stappen.
    + De passthru_automl_config vereiste voor het gebruik van tussenliggende gegevens met AutoMLStep in pijp lijnen is verwijderd.
  + **azureml-telemetry**
    + Document verbeteringen voor azureml-telemetrie.
  + **azureml-train-automl-client**
    + Er is een fout opgelost waarbij `experiment.submit()` twee keer voor een object wordt aangeroepen, wat een `AutoMLConfig` ander gedrag heeft veroorzaakt.
    + Gebruikers kunnen nu stack-ensemble-herhaling voor tijdreeks taken inschakelen met een waarschuwing dat het mogelijk is overfit.
    + Gedrag voor AutoML-uitvoering gewijzigd om UserErrorException te verhogen als de service een gebruikers fout genereert
    + Hiermee wordt een bug opgelost die ervoor heeft gezorgd dat azureml_automl. log niet wordt gegenereerd of Logboeken mist wanneer een AutoML-experiment wordt uitgevoerd op een extern Compute-doel.
    + Voor classificatie gegevens sets met niet-gebalanceerde klassen zullen we gewichts verdeling Toep assen als de functie Sweeper bepaalt dat voor subvoorbeeld gegevens de prestaties van de classificatie taak met een bepaalde drempel waarde worden verbeterd.
    + AutoML-uitvoeringen zijn nu gemarkeerd als onderliggend uitvoeren van de stap parallel uitvoeren.
  + **azureml-train-automl-runtime**
    + Gedrag voor AutoML-uitvoering gewijzigd om UserErrorException te verhogen als de service een gebruikers fout genereert
    + AutoML-uitvoeringen zijn nu gemarkeerd als onderliggend uitvoeren van de stap parallel uitvoeren.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK voor python v-1.7.0

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Het verwijderen van model profileren uit Mir contrib is voltooid door de CLI-opdrachten en pakket afhankelijkheden op te schonen. model Profiler is in core beschikbaar.
    + Hiermee wordt de minimale Azure CLI-versie bijgewerkt naar 2.3.0
  + **azureml-automl-core**
    + Betere uitzonderings bericht in parametrisatie stap fit_transform () vanwege aangepaste transformator parameters.
    + Voeg ondersteuning toe voor meerdere talen voor diep gaande modellen, zoals BERT in automatische MILLILITERs.
    + De afgeschafte lag_length-para meter uit de documentatie verwijderen.
    + De documentatie voor de prognose parameters is verbeterd. De para meter lag_length is afgeschaft.
  + **azureml-automl-runtime**
    + De fout wordt opgelost wanneer een van de categorische-kolommen leeg is in de prognose-en test tijd.
    + Los de uitvoerings fouten op wanneer de lookback-functies zijn ingeschakeld en de gegevens korte korrels bevatten.
    + Het probleem met gedupliceerd time-index fout bericht wordt opgelost wanneer lags of Rolling Windows is ingesteld op ' auto '.
    + U hebt het probleem opgelost met Prophet-en Arima-modellen in gegevens sets, die de lookback-functies bevatten.
    + Toegevoegde data van datums vóór 1677-09-21 of na 2262-04-11 in kolommen overige en vervolgens de datum en tijd in de prognose taken. Verbeterde fout berichten.
    + De documentatie voor de prognose parameters is verbeterd. De para meter lag_length is afgeschaft.
    + Betere uitzonderings bericht in parametrisatie stap fit_transform () vanwege aangepaste transformator parameters.
    + Voeg ondersteuning toe voor meerdere talen voor diep gaande modellen, zoals BERT in automatische MILLILITERs.
    + Bij cache bewerkingen die resulteren in een aantal OSErrors wordt de gebruikers fout gegenereerd.
    + Er zijn controles toegevoegd om te zorgen dat training-en validatie gegevens hetzelfde aantal en een set kolommen hebben
    + Probleem opgelost met het automatisch gegenereerde AutoML-Score script wanneer de gegevens aanhalings tekens bevatten
    + Het inschakelen van uitleg voor AutoML Prophet en ensembleed modellen die Prophet model bevatten.
    + Een recent probleem van een klant onthulde een live-site fout, waarbij we berichten volgen langs de cursus over het opruimen van klassen, zelfs wanneer de logica voor de verdeling van de klasse niet correct is ingeschakeld. De logboeken/berichten met deze PR verwijderen.
  + **azureml-cli-algemeen**
    + Het verwijderen van model profileren uit Mir contrib is voltooid door de CLI-opdrachten en pakket afhankelijkheden op te schonen. model Profiler is in core beschikbaar.
  + **azureml-contrib-reinforcementlearning**
    + Hulp programma belasting testen
  + **azureml-core**
    + Wijzigingen in de documentatie op Script_run_config. py
    + Hiermee wordt een bug opgelost met het afdrukken van de uitvoer van de verzenden-pipeline-CLI
    + Verbeterde documentatie voor azureml-core/azureml. data
    + Oplossen van problemen bij het ophalen van een opslag account met de opdracht hdfs getconf
    + Verbeterde documentatie voor register_azure_blob_container en register_azure_file_share
  + **azureml-datadrift**
    + Verbeterde implementatie voor het uitschakelen en inschakelen van dataset drift monitors
  + **azureml-interpret**
    + In uitleg client verwijdert u NaNs of inf-bestanden vóór JSON-serialisatie bij het uploaden van artefacten
    + Bijwerken naar de meest recente versie van interpreteren-Community voor het verbeteren van geheugen fouten voor globale toelichtingen met veel functies en klassen
    + Voeg true_ys optionele para meter toe aan het uploaden van uitleg om extra functies in de Studio-gebruikers interface in te scha kelen
    + Verbeter de prestaties van download_model_explanations () en list_model_explanations ()
    + Kleine aanpassingen aan notebooks, voor hulp bij fout opsporing
  + **azureml-opendatasets**
    + voor azureml-opendatapreps is de versie 1.4.0 of hoger van azureml vereist. Er is een waarschuwing toegevoegd als een lagere versie wordt gedetecteerd
  + **azureml-pipeline-core**
    + Met deze wijziging kan de gebruiker een optioneel runconfig opgeven voor de moduleVersion bij het aanroepen van de module. Publish_python_script.
    + Het inschakelen van knooppunt accounts kan een pijplijn parameter zijn in ParallelRunStep in azureml. pipeline. stappen
  + **azureml-pipeline-steps**
    + Met deze wijziging kan de gebruiker een optioneel runconfig opgeven voor de moduleVersion bij het aanroepen van de module. Publish_python_script.
  + **azureml-train-automl-client**
    + Voeg ondersteuning toe voor meerdere talen voor diep gaande modellen, zoals BERT in automatische MILLILITERs.
    + De afgeschafte lag_length-para meter uit de documentatie verwijderen.
    + De documentatie voor de prognose parameters is verbeterd. De para meter lag_length is afgeschaft.
  + **azureml-train-automl-runtime**
    + Het inschakelen van uitleg voor AutoML Prophet en ensembleed modellen die Prophet model bevatten.
    + Documentatie-updates voor azureml-Train-automl-*-pakketten.
  + **azureml-train-core**
    + Ondersteuning voor tensor flow-versie 2,1 in de PyTorch-estimator
    + Verbeteringen aan het pakket voor de azureml-trein kern.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK voor python v-1.6.0

+ **Nieuwe functies**
  + **azureml-automl-runtime**
    + AutoML-prognoses bieden nu ondersteuning voor klanten prognose buiten het vooraf opgegeven maximum-horizon zonder het model opnieuw te trainen. Wanneer de Voorspellings bestemming zich verder in de toekomst bevindt dan de opgegeven maximum horizon, maakt de functie Forecast () nog steeds voor speld naar de latere datum met behulp van een recursieve bewerkings modus. Voor de afbeelding van de nieuwe functie, zie de sectie ' prognoses verder dan de maximum Horizon ' van ' prognose maken-functie ' Notebook ' in de [map](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)'.
  
  + **azureml-pipeline-steps**
    + ParallelRunStep is nu uitgebracht en maakt deel uit van het pakket met **azureml-pipelines** . Bestaande ParallelRunStep in **azureml-contrib-pijp lijn-stappen** pakket is afgeschaft. Wijzigingen in de open bare preview-versie:
      + `run_max_try`De optionele Configureer bare para meter voor het beheren van de maximale aanroep naar de uitvoerings methode voor een bepaalde batch is toegevoegd. de standaard waarde is 3.
      + Er zijn geen PipelineParameters meer automatisch gegenereerd. De volgende Configureer bare waarden kunnen expliciet worden ingesteld als PipelineParameter.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + De standaard waarde voor process_count_per_node is gewijzigd in 1. De gebruiker moet deze waarde afstemmen voor betere prestaties. Aanbevolen procedure is om in te stellen als het aantal GPU-of CPU-knoop punt.
      + ParallelRunStep geen pakketten injecteert, moet de gebruiker de pakketten voor **azureml-core** en **azureml-dataprep [Pandas, zeker]** opnemen in de omgevings definitie. Als de aangepaste docker-installatie kopie wordt gebruikt met user_managed_dependencies moet de gebruiker Conda installeren op de installatie kopie.
      
+ **Wijzigingen die fouten veroorzaken**
  + **azureml-pipeline-steps**
    + Het gebruik van azureml. dprep. gegevensstroom is afgeschaft als een geldig type invoer voor AutoMLConfig
  + **azureml-train-automl-client**
    + Het gebruik van azureml. dprep. gegevensstroom is afgeschaft als een geldig type invoer voor AutoMLConfig

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + De bug opgelost waarbij een waarschuwing kan worden afgedrukt tijdens `get_output` de gebruiker om de client te downgradeen.
    + Mac bijgewerkt om te vertrouwen op cudatoolkit = 9.0, omdat deze nog niet beschikbaar is op versie 10.
    + Beperkingen voor Prophet-en xgboost-modellen verwijderen bij getraind op externe compute.
    + Verbeterde logboek registratie in AutoML
    + De fout afhandeling voor aangepaste parametrisatie in prognose taken is verbeterd.
    + Er is functionaliteit toegevoegd om gebruikers toe te staan getraagde functies te gebruiken voor het genereren van prognoses.
    + Hiermee wordt het fout bericht bijgewerkt zodat de gebruikers fout correct wordt weer gegeven.
    + Ondersteuning voor cv_split_column_names die worden gebruikt met training_data
    + Werk de logboek registratie van het uitzonderings bericht en traceback.
  + **azureml-automl-runtime**
    + Schakel Guardrails in voor het voors pellen van ontbrekende waarde-toerekeningen.
    + Verbeterde logboek registratie in AutoML
    + Nauw keurige gekorrelde fout afhandeling toegevoegd voor uitzonde ringen voor gegevens voorbereiding
    + Beperkingen voor phrophet-en xgboost-modellen verwijderen bij getraind op externe compute.
    + `azureml-train-automl-runtime` en `azureml-automl-runtime` hebben bijgewerkte afhankelijkheden voor `pytorch` , `scipy` , en `cudatoolkit` . We ondersteunen nu `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` en `cudatoolkit==10.1.243` .
    + De fout afhandeling voor aangepaste parametrisatie in prognose taken is verbeterd.
    + Het mechanisme voor het detecteren van de frequentie van gegevens sets is verbeterd.
    + Probleem opgelost met Prophet model training voor sommige gegevens sets.
    + De automatische detectie van de maximale horizon tijdens de prognose is verbeterd.
    + Er is functionaliteit toegevoegd om gebruikers toe te staan getraagde functies te gebruiken voor het genereren van prognoses.
    +  Voegt functionaliteit toe aan de functie Forecast om prognoses te bieden buiten de getrainde horizon zonder het prognose model opnieuw te trainen.
    + Ondersteuning voor cv_split_column_names die worden gebruikt met training_data
  + **azureml-contrib-automl-DNN-Forecasting**
    + Verbeterde logboek registratie in AutoML
  + **azureml-contrib-Mir**
    + Er is ondersteuning toegevoegd voor Windows-Services in ManagedInferencing
    + Oude MIR-werk stromen verwijderen, zoals MIR Compute koppelen, SingleModelMirWebservice klasse-opschonen model Profiler geplaatst in contrib-Mir-pakket
  + **azureml-contrib-pipeline-steps**
    + Kleine oplossing voor YAML-ondersteuning
    + ParallelRunStep wordt uitgebracht voor algemene Beschik baarheid-azureml. contrib. pipeline. stappen hebben een kennisgeving van afschaffing en worden verplaatst naar azureml. pipeline. stappen
  + **azureml-contrib-reinforcementlearning**
    + Hulp programma voor het testen van RL-belasting
    + RL Estimator heeft slimme standaard waarden
  + **azureml-core**
    + Oude MIR-werk stromen verwijderen, zoals MIR Compute koppelen, SingleModelMirWebservice klasse-opschonen model Profiler geplaatst in contrib-Mir-pakket
    + De gegevens die aan de gebruiker zijn verstrekt, zijn in het geval van een profilerings fout opgelost: de opgenomen aanvraag-ID en het bericht is opnieuw ingesteld voor een duidelijkere betekenis. Er is een nieuwe profilerings werk stroom toegevoegd voor het profileren van uitlopers
    + Verbeterde fout tekst in het geval van fouten bij de uitvoering van een gegevensset.
    + De ondersteuning voor de werk ruimte-CLI is toegevoegd.
    + Er is een optionele para meter toegevoegd `invalid_lines` aan `Dataset.Tabular.from_json_lines_files` waarmee kan worden opgegeven hoe regels worden verwerkt die een ongeldige JSON bevatten.
    + In de volgende release wordt het maken van een compute-bewerking voor komen. U kunt het beste een werkelijk Amlcompute-cluster maken als een persistent Compute-doel en de cluster naam gebruiken als het reken doel in uw uitvoerings configuratie. Bekijk hier een voor beeld van een notitie blok: aka.ms/amlcomputenb
    + Verbeterde fout berichten in het geval van fouten bij de uitvoering van een gegevensset.
  + **azureml-dataprep**
    + Er is een waarschuwing gemaakt om de pyarrow-versie meer expliciet te upgraden.
    + Verbeterde fout afhandeling en geretourneerd bericht in het geval van een fout tijdens het uitvoeren van de gegevens stroom.
  + **azureml-interpret**
    + Documentatie-updates voor pakketten met azureml-interpretatie.
    + Pakketten en notebooks met vaste interpretaties kunnen compatibel zijn met de nieuwste sklearn-update
  + **azureml-opendatasets**
    + Geen retour waarde wanneer er geen gegevens zijn geretourneerd.
    + Verbeter de prestaties van to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Snelle oplossing voor ParallelRunStep, waarbij het laden van YAML is verbroken
    + ParallelRunStep wordt uitgebracht voor algemene Beschik baarheid-azureml. contrib. pipeline. stappen hebben een kennisgeving met betrekking tot de afschaffing. pijp lijn. stappen: nieuwe functies zijn: 1. Gegevens sets als PipelineParameter 2. Nieuwe para meter run_max_retry 3. Configureer bare append_row uitvoer bestands naam
  + **azureml-pipeline-steps**
    + Verouderd azureml. dprep. gegevensstroom als een geldig type voor invoer gegevens.
    + Snelle oplossing voor ParallelRunStep, waarbij het laden van YAML is verbroken
    + ParallelRunStep wordt uitgebracht voor algemene Beschik baarheid-azureml. contrib. pipeline. stappen hebben een kennisgeving van afschaffing en worden verplaatst naar azureml. pipeline. stappen-nieuwe functies zijn:
      + Gegevens sets als PipelineParameter
      + Nieuwe para meter run_max_retry
      + Configureer bare append_row uitvoer bestands naam
  + **azureml-telemetry**
    + Werk de logboek registratie van het uitzonderings bericht en traceback.
  + **azureml-train-automl-client**
    + Verbeterde logboek registratie in AutoML
    + Hiermee wordt het fout bericht bijgewerkt zodat de gebruikers fout correct wordt weer gegeven.
    + Ondersteuning voor cv_split_column_names die worden gebruikt met training_data
    + Verouderd azureml. dprep. gegevensstroom als een geldig type voor invoer gegevens.
    + Mac bijgewerkt om te vertrouwen op cudatoolkit = 9.0, omdat deze nog niet beschikbaar is op versie 10.
    + Beperkingen voor phrophet-en xgboost-modellen verwijderen bij getraind op externe compute.
    + `azureml-train-automl-runtime` en `azureml-automl-runtime` hebben bijgewerkte afhankelijkheden voor `pytorch` , `scipy` , en `cudatoolkit` . We ondersteunen nu `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` en `cudatoolkit==10.1.243` .
    + Er is functionaliteit toegevoegd om gebruikers toe te staan getraagde functies te gebruiken voor het genereren van prognoses.
  + **azureml-train-automl-runtime**
    + Verbeterde logboek registratie in AutoML
    + Nauw keurige gekorrelde fout afhandeling toegevoegd voor uitzonde ringen voor gegevens voorbereiding
    + Beperkingen voor phrophet-en xgboost-modellen verwijderen bij getraind op externe compute.
    + `azureml-train-automl-runtime` en `azureml-automl-runtime` hebben bijgewerkte afhankelijkheden voor `pytorch` , `scipy` , en `cudatoolkit` . We ondersteunen nu `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` en `cudatoolkit==10.1.243` .
    + Hiermee wordt het fout bericht bijgewerkt zodat de gebruikers fout correct wordt weer gegeven.
    + Ondersteuning voor cv_split_column_names die worden gebruikt met training_data
  + **azureml-train-core**
    + Er is een nieuwe set HyperDrive-specifieke uitzonde ringen toegevoegd. in azureml. Train. Hyperdrive worden nu gedetailleerde uitzonde ringen gegenereerd.
  + **azureml-widgets**
    + AzureML-widgets worden niet weer gegeven in Jjupyterlab
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Azure Machine Learning SDK voor python v-1.5.0

+ **Nieuwe functies**
  + **Preview-functies**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning geeft preview-ondersteuning weer voor het versterken van learning met behulp van het [Ray](https://ray.io) -Framework. `ReinforcementLearningEstimator`Hiermee wordt getraind voor het trainen van Learning-agents op GPU-en CPU-Compute-doelen in azure machine learning.

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Hiermee wordt een per ongeluk achtergebleven waarschuwings logboek in mijn vorige PR opgelost. Het logboek is gebruikt voor fout opsporing en er is per ongeluk achter gelaten.
    + Fout oplossing: clients informeren over gedeeltelijk fout tijdens profilering
  + **azureml-automl-core**
    + Prophet/AutoArima model versnellen in AutoML-prognoses door parallelle montage in te scha kelen voor de tijd reeks wanneer gegevens sets meerdere tijd reeksen hebben. Als u wilt profiteren van deze nieuwe functie, kunt u het beste ' max_cores_per_iteration =-1 ' (dat wil zeggen, met behulp van alle beschik bare CPU-kernen) in AutoMLConfig instellen.
    + Fout bij het afdrukken van Guardrails in de console-interface oplossen
    + Vast fout bericht voor experimentation_timeout_hours
    + Afgeschafte tensor flow-modellen voor AutoML.
  + **azureml-automl-runtime**
    + Vast fout bericht voor experimentation_timeout_hours
    + Er is een niet-geclassificeerde uitzonde ring opgetreden bij het deserialiseren van de cache opslag
    + Prophet/AutoArima model versnellen in AutoML-prognoses door parallelle montage in te scha kelen voor de tijd reeks wanneer gegevens sets meerdere tijd reeksen hebben.
    + Er is een probleem opgelost met het ingeschakelde Rolling venster voor de gegevens sets waarbij de test/Voorspellings geen korrels uit de Trainingsset bevat.
    + Verbeterde verwerking van ontbrekende gegevens
    + Er is een probleem opgelost met Voorspellings intervallen tijdens het voors pellen van gegevens sets met een tijd reeks die niet op tijd is uitgelijnd.
    + Er is een betere validatie van de gegevens vorm toegevoegd voor de prognose taken.
    + De frequentie detectie is verbeterd.
    + Er is een beter fout bericht gemaakt als de Kruis validatie vouwen voor prognose taken niet kunnen worden gegenereerd.
    + Herstel de console interface om de waarde guardrail correct af te drukken.
    + Het afdwingen van gegevens type controles op cv_split_indices invoer in AutoMLConfig.
  + **azureml-cli-algemeen**
    + Fout oplossing: clients informeren over gedeeltelijk fout tijdens profilering
  + **azureml-contrib-Mir**
    + Hiermee wordt een klasse van azureml. contrib. Mir. RevisionStatus die informatie over de momenteel geïmplementeerde MIR revisie en de meest recente versie die door de gebruiker is opgegeven, doorstuurt. Deze klasse is opgenomen in het MirWebservice-object onder het kenmerk deployment_status.
    + Hiermee wordt update ingeschakeld voor webservices van het type MirWebservice en de onderliggende klasse SingleModelMirWebservice.
  + **azureml-contrib-reinforcementlearning**
    + Ondersteuning toegevoegd voor Ray 0.8.3
    + AmlWindowsCompute biedt alleen ondersteuning voor Azure Files als gekoppelde opslag
    + De naam van health_check_timeout gewijzigd in health_check_timeout_seconds
    + Er zijn een aantal beschrijvingen van klassen/methoden opgelost.
  + **azureml-core**
    + Ingeschakelde WASB-> BLOB-conversies in Azure Government-en China-Clouds.
    + Lost een fout op zodat lezers rollen kunnen gebruiken AZ ml run CLI-opdrachten om uitvoerings gegevens op te halen
    + Overbodige logboek registratie verwijderd tijdens Azure ML externe uitvoeringen met invoer gegevens sets.
    + RCranPackage ondersteunt nu de para meter "version" voor de versie van het KRANs pakket.
    + Fout oplossing: clients informeren over gedeeltelijk fout tijdens profilering
    + De verwerking van Europese stijlen met drijvende komma is toegevoegd voor de azureml-kern.
    + Ingeschakelde werk ruimte functies voor persoonlijke koppelingen in azure ml SDK.
    + Wanneer u een TabularDataset maakt met `from_delimited_files` , kunt u opgeven of lege waarden moeten worden geladen als geen of als lege teken reeks door het Booleaanse argument in te stellen `empty_as_string` .
    + De verwerking van Europese stijlen voor gegevens sets is toegevoegd.
    + Verbeterde fout berichten bij het koppelen van fouten in een gegevensset.
  + **azureml-datadrift**
    + De resultaten query van de gegevens drift van de SDK bevat een fout die de metrische waarden van de functie minimum, maximum en gemiddelde niet onderscheidt, wat resulteert in dubbele waarde. Deze fout is opgelost door het doel of de basis lijn voor de metrische namen voor te stellen. Vóór: dubbele min, maximum, gemiddelde. Na: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + De verwerking van Beschrijf bare python-omgevingen verbeteren wanneer .NET-afhankelijkheden vereist zijn voor het leveren van gegevens.
    + Het maken van een vaste gegevensstroom voor een bestand met voorloop lege records.
    + Er zijn opties voor fout afhandeling toegevoegd voor `to_partition_iterator` vergelijkbaar met `to_pandas_dataframe` .
  + **azureml-interpret**
    + Beperkte lengte limieten voor het pad van de uitleg om de kans op een continue Windows-limiet te verminderen
    + Bugfix voor zeldzame uitleg gemaakt met behulp van een lineair surrogaat model met de gesimuleerde uitleger.
  + **azureml-opendatasets**
    + Het oplossen van het probleem van de kolommen van de MNIST wordt geparseerd als teken reeks, die int moet zijn.
  + **azureml-pipeline-core**
    + De optie voor het regenerate_outputs van het gebruik van een module die is inge sloten in een ModuleStep.
  + **azureml-train-automl-client**
    + Afgeschafte tensor flow-modellen voor AutoML.
    + Gebruikers herstellen toestaan dat niet-ondersteunde algoritmen in de lokale modus worden vermeld
    + Doc is opgelost in AutoMLConfig.
    + Het afdwingen van gegevens type controles op cv_split_indices invoer in AutoMLConfig.
    + Probleem opgelost met AutoML-uitvoeringen mislukt in show_output
  + **azureml-train-automl-runtime**
    + Het oplossen van een bug in ensemble-iteraties waardoor de time-out voor het downloaden van modellen niet kan worden gestart.
  + **azureml-train-core**
    + Corrigeer de type fout in de klasse azureml. Train. DNN. Nccl.
    + Ondersteuning voor PyTorch-versie 1,5 in de PyTorch-estimator
    + Het probleem oplossen dat de Framework-afbeelding niet kan worden opgehaald in Azure Government regio bij het gebruik van schattingen van trainings raamwerken

  
## <a name="2020-05-04"></a>2020-05-04
**Nieuwe laptop ervaring**

U kunt nu machine learning-notitie blokken en bestanden rechtstreeks maken, bewerken en delen in de Studio Web Experience van Azure Machine Learning. U kunt alle klassen en methoden die beschikbaar zijn in [Azure machine learning python-SDK](/python/api/overview/azure/ml/intro) van deze notitie blokken gebruiken om [hier](./how-to-run-jupyter-notebooks.md) aan de slag te gaan

**Nieuwe functies die zijn geïntroduceerd:**

+ Verbeterde editor (Monaco-editor) gebruikt door VS code 
+ Verbeteringen in gebruikers interface/UX
+ Werk balk cel
+ Nieuwe werk balk van notitie blok en reken besturings elementen
+ Status balk van notitie blok 
+ Overschakelen van inline-kernel
+ R-ondersteuning
+ Verbeterde toegankelijkheid en lokalisatie
+ Opdracht palet
+ Aanvullende sneltoetsen
+ Opslaan
+ Verbeterde prestaties en betrouw baarheid

Toegang tot de volgende webgebaseerde hulp middelen voor ontwerpen vanuit Studio:
    
| Webgebaseerd hulp programma  |     Beschrijving  |
|---|---|
| Azure ML Studio-notebooks   |     Eerste in-class ontwerpen voor notebook-bestanden en biedt ondersteuning voor alle bewerkingen die beschikbaar zijn in de Azure ML python SDK. | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK voor python v-1.4.0

+ **Nieuwe functies**
  + AmlCompute-clusters bieden nu ondersteuning voor het instellen van een beheerde identiteit op het cluster op het moment van de inrichting. Geef aan of u een door het systeem toegewezen identiteit of een door de gebruiker toegewezen identiteit wilt gebruiken en geef een identityId voor de laatste door. U kunt vervolgens machtigingen instellen voor toegang tot verschillende bronnen, zoals opslag of ACR, op een manier dat de identiteit van de compute wordt gebruikt om veilig toegang te krijgen tot de gegevens, in plaats van een op tokens gebaseerde benadering die AmlCompute maakt. Bekijk onze SDK-referentie voor meer informatie over de para meters.
  

+ **Wijzigingen die fouten veroorzaken**
  + AmlCompute-clusters ondersteunen een preview-functie voor het maken van een uitvoering, die we in twee weken plannen. U kunt persistente Compute-doelen maken als always met behulp van de Amlcompute-klasse, maar de specifieke benadering van het opgeven van de id ' Amlcompute ' als het reken doel in run config wordt niet in de nabije toekomst ondersteund. 

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-runtime**
    + Ondersteuning inschakelen voor het type unhashable bij het berekenen van het aantal unieke waarden in een kolom.
  + **azureml-core**
    + Verbeterde stabiliteit bij het lezen van Azure Blob Storage met behulp van een TabularDataset.
    + Verbeterde documentatie voor de `grant_workspace_msi` para meter voor `Datastore.register_azure_blob_store` .
    + Er is een fout opgelost met `datastore.upload` ter ondersteuning van het `src_dir` argument dat eindigt op een `/` of `\` .
    + Er is een fout bericht toegevoegd dat kan worden uitgevoerd bij het uploaden naar een Azure Blob Storage-gegevens opslag dat geen toegangs sleutel of SAS-token heeft.
  + **azureml-interpret**
    + Er is een bovengrens voor de bestands grootte van de visualisatie gegevens op geüploade uitleg toegevoegd.
  + **azureml-train-automl-client**
    + Expliciet controleren op label_column_name & weight_column_name para meters voor AutoMLConfig is van het type teken reeks.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep ondersteunt nu dataset als pijplijn parameter. De gebruiker kan een pijp lijn maken met de voorbeeld gegevensset en kan de invoer gegevensset van hetzelfde type (bestand of tabellaire) wijzigen voor een nieuwe pijplijn uitvoering.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK voor python v-1.3.0

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + Er is extra telemetrie toegevoegd rondom post training-bewerkingen.
    + Versnelt de automatische ARIMA-training door gebruik te maken van de training van een voorwaardelijke som van kwadraten (CSS) voor een reeks lengte van meer dan 100. De gebruikte lengte wordt opgeslagen als constante ARIMA_TRIGGER_CSS_TRAINING_LENGTH w/in de klasse TimeSeriesInternal bij/src/azureml-automl-core/azureml/automl/core/Shared/Constants.py
    + De gebruikers registratie van prognose-uitvoeringen is verbeterd, nu meer informatie over welke fase momenteel wordt uitgevoerd, wordt weer gegeven in het logboek
    + Niet toegestaan target_rolling_window_size worden ingesteld op waarden die kleiner zijn dan 2
  + **azureml-automl-runtime**
    + Het fout bericht dat wordt weer gegeven wanneer dubbele tijds tempels worden gevonden, is verbeterd.
    + Niet toegestaan target_rolling_window_size worden ingesteld op waarden die kleiner zijn dan 2.
    + Er is een fout opgetreden bij het weglaten van vertraging. Het probleem wordt veroorzaakt door het onvoldoende aantal waarnemingen dat nodig is voor het seizoen afbreken van een reeks. De gegevens ' onbepaald ' worden gebruikt voor het berekenen van een gedeeltelijke autocorrelation-functie (PACF) om de lengte van de vertraging te bepalen.
    + Parametrisatie aanpassing van het kolom doel voor de prognose van taken door parametrisatie config. De numerieke en categorische als kolom doel voor prognose taken worden nu ondersteund.
    + Ingeschakelde drop column parametrisatie aanpassing voor prognose taken door parametrisatie config.
    + Aanpassing van de toerekening ingeschakeld voor prognose taken door parametrisatie config. De toewijzing van constante waarden voor de doel kolom en gemiddelde, mediaan, most_frequent en constante waarde toerekening voor trainings gegevens wordt nu ondersteund.
  + **azureml-contrib-pipeline-steps**
    + Teken reeks Compute-namen accepteren die moeten worden door gegeven aan ParallelRunConfig
  + **azureml-core**
    +  API (Environment. kloon) (new_name) is toegevoegd voor het maken van een kopie van een omgevings object
    +  Environment.docker.base_dockerfile het bestandspad accepteert. Als een bestand kan worden omgezet, wordt de inhoud in base_dockerfile omgevings eigenschap gelezen.
    + Elkaar wederzijds exclusieve waarden voor base_image automatisch opnieuw instellen en base_dockerfile wanneer gebruiker hand matig een waarde instelt in Environment.docker
    + User_managed vlag toegevoegd in RSection die aangeeft of de omgeving wordt beheerd door de gebruiker of door AzureML.
    + Gegevensset: fout bij het downloaden van de vaste gegevensset als het gegevenspad Unicode-tekens bevat.
    + Gegevensset: verbeterd cache mechanisme voor het koppelen van de gegevensset om te voldoen aan de minimale schijfruimte vereiste in Azure Machine Learning compute, waarmee wordt voor komen dat het knoop punt onbruikbaar wordt gemaakt en de taak wordt geannuleerd.
    + Gegevensset: we voegen een index voor de kolom time series toe wanneer u een time series-gegevensset opent als een Panda data frame, die wordt gebruikt om de toegang tot gegevens op basis van een tijd reeks sneller te maken.  Voorheen werd de index dezelfde naam gegeven als de time stamp-kolom, waardoor gebruikers die de werkelijke time stamp-kolom bevinden en de index zijn. We geven de index nu geen specifieke naam, aangezien deze niet als kolom mag worden gebruikt. 
    + Gegevensset: probleem met vaste gegevensset-verificatie in soevereine Cloud.
    + Gegevensset: er is een `Dataset.to_spark_dataframe` fout opgelost voor gegevens sets die zijn gemaakt op basis van Azure postgresql-gegevens opslag.
  + **azureml-interpret**
    + Globale scores toegevoegd aan visualisatie als lokale urgentie waarden verspreid zijn
    + Update voor azureml-interpret voor gebruik van interpretatie-Community 0,9. *
    + Probleem opgelost met het downloaden van een uitleg die verspreide evaluatie gegevens had
    + Ondersteuning toegevoegd voor de sparse-indeling van het object uitleg in AutoML
  + **azureml-pipeline-core**
    + ComputeInstance als Compute-doel in pijp lijnen ondersteunen
  + **azureml-train-automl-client**
    + Er is extra telemetrie toegevoegd rondom post training-bewerkingen.
    + De regressie in vroege stop opgelost
    + Verouderd azureml. dprep. gegevensstroom als een geldig type voor invoer gegevens.
    +  De standaard tijd van AutoML-experimenten wijzigen tot zes dagen.
  + **azureml-train-automl-runtime**
    + Er is extra telemetrie toegevoegd rondom post training-bewerkingen.
    + ondersteuning voor sparse AutoML end-to-end is toegevoegd
  + **azureml-opendatasets**
    + Er is extra telemetrie toegevoegd voor service monitor.
    + Voor deur inschakelen voor blob om de stabiliteit te verhogen 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK voor python v-1.2.0

+ **Wijzigingen die fouten veroorzaken**
  + Ondersteuning voor python 2,7 door halen

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Voegt '--subscription-id ' toe aan `az ml model/computetarget/service` de opdrachten in de cli
    + Ondersteuning toevoegen voor het door geven van door de klant beheerde sleutel (CMK) vault_url, key_name en key_version voor ACI-implementatie
  + **azureml-automl-core** 
    + Aangepaste toerekening met constante waarde voor taak prognose van X-en y-gegevens is ingeschakeld.
    + Het probleem is opgelost in met het weer geven van fout berichten aan de gebruiker.    
  + **azureml-automl-runtime**
    + Het probleem is opgelost in met prognoses voor de gegevens sets met granen met slechts één rij
    + De hoeveelheid geheugen die nodig is voor de prognose taken is verminderd.
    + Er zijn betere fout berichten toegevoegd als de tijd kolom een onjuiste indeling heeft.
    + Aangepaste toerekening met constante waarde voor taak prognose van X-en y-gegevens is ingeschakeld.
  + **azureml-core**
    + Er is ondersteuning toegevoegd voor het laden van ServicePrincipal van omgevings variabelen: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID en AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Heeft een nieuwe para meter geïntroduceerd `support_multi_line` voor `Dataset.Tabular.from_delimited_files` : standaard ( `support_multi_line=False` ), alle regel einden, inclusief de waarden in de velden met een aanhalings teken, worden geïnterpreteerd als een record-einde. Het lezen van gegevens op deze manier is sneller en geoptimaliseerd voor parallelle uitvoering op meerdere CPU-kernen. Het kan echter leiden tot het op de achtergrond genereren van meer records met onjuist uitgelijnde veld waarden. Dit moet worden ingesteld op `True` wanneer de bestanden met scheidings tekens bekend zijn met geciteerde regel einden.
    + De mogelijkheid om ADLS Gen2 te registreren in de Azure Machine Learning CLI is toegevoegd
    + De naam van de para meter fine_grain_timestamp is gewijzigd in tijds tempel en de para meter coarse_grain_timestamp naar partition_timestamp voor de methode with_timestamp_columns () in TabularDataset om het gebruik van de para meters beter weer te geven.
    + De maximale lengte van de experiment naam is verhoogd tot 255.
  + **azureml-interpret**
    + Updated azureml-interprete voor interprete-Community 0,7. *
  + **azureml-SDK**
    + Het wijzigen van afhankelijkheden met compatibele versie-tilde voor de ondersteuning van patches van voorlopige versies en stabiele releases.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK voor python v-1.1.5

+ **Afschaffing van functies**
  + **Python 2,7**
    + Laatste versie voor de ondersteuning van python 2,7

+ **Wijzigingen die fouten veroorzaken**
  + **Semantic Versioning 2.0.0**
    + Vanaf versie 1,1 Azure ML python SDK worden semantische versie beheer 2.0.0. [Meer informatie vindt u hier](https://semver.org/). Alle volgende versies volgen een nieuw Nummerings schema en een contract voor semantische versie. 

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Wijzig de naam van de eindpunt CLI-opdracht van ' AZ ml endpoint AKS ' in ' AZ ml endpoint real time ' voor consistentie.
    + CLI-installatie-instructies bijwerken voor stabiele en experimentele vertakking CLI
    + Profileren van één exemplaar is opgelost om een aanbeveling te doen en is beschikbaar gesteld in de core-SDK.
  + **azureml-automl-core**
    + De batch modus in te scha kelen (meerdere rijen nemen) voor AutoML ONNX-modellen
    + Verbeterde detectie van de frequentie van de gegevens sets, ontbrekende gegevens of met onregelmatige gegevens punten
    + De mogelijkheid om gegevens punten te verwijderen die niet voldoen aan de dominante frequentie is toegevoegd.
    + De invoer van de constructor is gewijzigd om een lijst met opties te maken voor het Toep assen van de toerekenings opties voor de bijbehorende kolommen.
    + De logboek registratie van fouten is verbeterd.
  + **azureml-automl-runtime**
    + Het probleem is opgelost met de fout die wordt gegenereerd als de korrel niet aanwezig is in de set training die in de testset is opgenomen
    + De y_query vereiste is verwijderd tijdens de score voor de prognose service
    + U hebt het probleem opgelost met prognoses wanneer de gegevensset korte korrels bevat met lange tijds intervallen.
    + Het probleem is opgelost wanneer de automatische maximum horizon is ingeschakeld en de datum kolom bevat datums in een vorm van teken reeksen. De juiste conversie en fout berichten zijn toegevoegd voor wanneer de conversie naar een datum niet mogelijk is
    + Systeem eigen NumPy en SciPy gebruiken voor het serialiseren en deserialiseren van tussenliggende gegevens voor file cache Store (gebruikt voor lokale AutoML-uitvoeringen)
    + Er is een fout opgelost waarbij mislukte onderliggende uitvoeringen in de actieve status kunnen raken.
    + Verhoogde snelheid van parametrisatie.
    + De frequentie controle tijdens de score is opgelost. voor de prognose taken is de frequentie-equivalentie tussen de trein-en testset nu niet vereist.
    + De invoer van de constructor is gewijzigd om een lijst met opties te maken voor het Toep assen van de toerekenings opties voor de bijbehorende kolommen.
    + Opgeloste fouten met betrekking tot selectie van vertragings type.
    + De niet-geclassificeerde fout is opgetreden op de gegevens sets, waarbij korrels met één rij
    + Het probleem met de vertraging van de frequentie detectie is opgelost.
    + Hiermee wordt een fout opgelost in de verwerking van AutoML-uitzonde ringen die de echte reden voor het mislukken van de training heeft veroorzaakt, worden vervangen door een AttributeError.
  + **azureml-cli-algemeen**
    + Profileren van één exemplaar is opgelost om een aanbeveling te doen en is beschikbaar gesteld in de core-SDK.
  + **azureml-contrib-Mir**
    + Voegt functionaliteit toe aan de klasse MirWebservice om het toegangs token op te halen
    + Token verificatie voor MirWebservice standaard gebruiken tijdens MirWebservice. run () alleen-aanroep vernieuwen als de aanroep mislukt
    + Voor de implementatie van de Mir-webservice zijn nu de juiste Sku's [Standard_DS2_v2, Standard_F16, Standard_A2_v2] vereist in plaats van respectievelijk [Ds2v2, A2v2 en F16].
  + **azureml-contrib-pipeline-steps**
    + Optionele para meter side_inputs toegevoegd aan ParallelRunStep. Deze para meter kan worden gebruikt om de map in de container te koppelen. De momenteel ondersteunde typen zijn DataReference en PipelineData.
    + Para meters die in ParallelRunConfig worden door gegeven, kunnen worden overschreven door de pijplijn parameters nu door te geven. Nieuwe pijplijn parameters die worden ondersteund aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count en aml_process_count_per_node maken al deel uit van een eerdere versie).
  + **azureml-core**
    + Met de geïmplementeerde AzureML-webservices wordt nu standaard `INFO` vastgelegd. Dit kan worden bepaald door de `AZUREML_LOG_LEVEL` omgevings variabele in de geïmplementeerde service in te stellen.
    + Python SDK gebruikt de detectie service voor het gebruik van het eind punt API in plaats van pijp lijnen.
    + Wisselen naar de nieuwe routes in alle SDK-aanroepen.
    + De route ring van aanroepen naar de ModelManagementService is gewijzigd in een nieuwe, geïntegreerde structuur.
      + De methode voor het bijwerken van de werk ruimte is openbaar beschikbaar gemaakt.
      + Er is een image_build_compute para meter toegevoegd aan de methode voor het bijwerken van de werk ruimte om de gebruiker toe te staan de compute voor de
    + Er zijn afschaffing-berichten toegevoegd aan de oude profilerings werk stroom. Vaste profilering CPU en geheugen limieten.
    + RSection toegevoegd als onderdeel van de omgeving voor het uitvoeren van R-taken.
    + Er is een validatie toegevoegd om een `Dataset.mount` fout te genereren als de bron van de gegevensset niet toegankelijk is of geen gegevens bevat.
    + Toegevoegd `--grant-workspace-msi-access` als een extra para meter voor de Data Store CLI voor het registreren van de Azure Blob-container, waarmee u een BLOB-container kunt registreren die zich achter een VNet bevindt.
    + Profileren van één exemplaar is opgelost om een aanbeveling te doen en is beschikbaar gesteld in de core-SDK.
    + Het probleem is opgelost in aks.py _deploy.
    + Valideert de integriteit van modellen die worden geüpload om problemen met de Stille opslag te voor komen.
    + De gebruiker kan nu een waarde opgeven voor de verificatie sleutel bij het opnieuw genereren van sleutels voor webservices.
    + Er is een fout opgelost waarbij hoofd letters niet kunnen worden gebruikt als de invoer naam van de gegevensset.
  + **azureml-standaards**
    + `azureml-dataprep` wordt nu geïnstalleerd als onderdeel van `azureml-defaults` . Het is niet meer nodig om data prep [zeker] hand matig te installeren op reken doelen om gegevens sets te koppelen.
  + **azureml-interpret**
    + Updated azureml-interprete voor interprete-Community 0,6. *
    + Bijgewerkt azureml-interpreteren om afhankelijk te zijn van interprete-Community 0.5.0
    + Uitzonde ringen voor azureml-stijl toegevoegd aan azureml-interprete
    + Vaste DeepScoringExplainer-serialisatie voor Keras-modellen
  + **azureml-mlflow**
    + Ondersteuning voor soevereine Clouds toevoegen aan azureml. mlflow
  + **azureml-pipeline-core**
    + Pipeline batch Score notebook maakt nu gebruik van ParallelRunStep
    + Er is een fout opgelost waarbij PythonScriptStep resultaten onjuist opnieuw kunnen worden gebruikt, ondanks het wijzigen van de lijst met argumenten
    + De mogelijkheid om kolommen in te stellen is toegevoegd tijdens het aanroepen van de parse_ *-methoden op `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + De `AutoMLStep` naar het pakket is verplaatst `azureml-pipeline-steps` . Afgeschaft `AutoMLStep` in `azureml-train-automl-runtime` .
    + Voor beeld van toegevoegde documentatie voor gegevensset als PythonScriptStep-invoer
  + **azureml-tensorboard**
    + update voor azureml-tensorboard om tensor Flow 2,0 te ondersteunen
    + Correct poort nummer weer geven bij het gebruik van een aangepaste Tensorboard-poort op een reken instantie
  + **azureml-train-automl-client**
    + Er is een probleem opgelost waarbij bepaalde pakketten op externe uitvoeringen op onjuiste versies kunnen worden geïnstalleerd.
    + Er is een vast FeaturizationConfig-probleem opgetreden waardoor aangepaste parametrisatie-configuratie wordt gefilterd.
  + **azureml-train-automl-runtime**
    + Het probleem met de frequentie detectie is opgelost in de externe uitvoeringen
    + De `AutoMLStep` in het pakket is verplaatst `azureml-pipeline-steps` . Afgeschaft `AutoMLStep` in `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Ondersteuning voor PyTorch-versie 1,4 in de PyTorch-estimator
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK voor python v 1.1.2 RC0 (voorlopige versie)

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + De batch modus in te scha kelen (meerdere rijen nemen) voor AutoML ONNX-modellen
    + Verbeterde detectie van de frequentie van de gegevens sets, ontbrekende gegevens of met onregelmatige gegevens punten
    + De mogelijkheid om gegevens punten te verwijderen die niet voldoen aan de dominante frequentie is toegevoegd.
  + **azureml-automl-runtime**
    + Het probleem is opgelost met de fout die wordt gegenereerd als de korrel niet aanwezig is in de set training die in de testset is opgenomen
    + De y_query vereiste is verwijderd tijdens de score voor de prognose service
  + **azureml-contrib-Mir**
    + Voegt functionaliteit toe aan de klasse MirWebservice om het toegangs token op te halen
  + **azureml-core**
    + Met de geïmplementeerde AzureML-webservices wordt nu standaard `INFO` vastgelegd. Dit kan worden bepaald door de `AZUREML_LOG_LEVEL` omgevings variabele in de geïmplementeerde service in te stellen.
    + Herstel herhalen op `Dataset.get_all` om alle gegevens sets te retour neren die zijn geregistreerd bij de werk ruimte.
    + Het fout bericht wordt verbeterd wanneer een ongeldig type wordt door gegeven aan het `path` argument van de API voor het maken van de gegevensset.
    + Python SDK gebruikt de detectie service voor het gebruik van het eind punt API in plaats van pijp lijnen.
    + Wisselen naar de nieuwe routes in alle SDK-aanroepen
    + Verandert de route ring van aanroepen naar de ModelManagementService naar een nieuwe, geïntegreerde structuur
      + De methode voor het bijwerken van de werk ruimte is openbaar beschikbaar gemaakt.
      + Er is een image_build_compute para meter toegevoegd aan de methode voor het bijwerken van de werk ruimte om de computer toe te staan om de compute
    +  Er zijn afschaffing-berichten toegevoegd aan de oude profilerings werk stroom. Vaste profilering CPU en geheugen limieten
  + **azureml-interpret**
    + Update azureml-interprete voor interprete-Community 0,6. *
  + **azureml-mlflow**
    + Ondersteuning voor soevereine Clouds toevoegen aan azureml. mlflow
  + **azureml-pipeline-steps**
    + Verplaatst `AutoMLStep` naar de `azureml-pipeline-steps package` . Afgeschaft `AutoMLStep` in `azureml-train-automl-runtime` .
  + **azureml-train-automl-client**
    + Er is een probleem opgelost waarbij bepaalde pakketten op externe uitvoeringen op onjuiste versies kunnen worden geïnstalleerd.
  + **azureml-train-automl-runtime**
    + Het probleem met de frequentie detectie is opgelost in de externe uitvoeringen
    + Verplaatst `AutoMLStep` naar de `azureml-pipeline-steps package` . Afgeschaft `AutoMLStep` in `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Verplaatst `AutoMLStep` naar de `azureml-pipeline-steps package` . Afgeschaft `AutoMLStep` in `azureml-train-automl-runtime` .

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK voor python v 1.1.1 RC0 (voorlopige versie)

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Profileren van één exemplaar is opgelost om een aanbeveling te doen en is beschikbaar gesteld in de core-SDK.
  + **azureml-automl-core**
    + De logboek registratie van fouten is verbeterd.
  + **azureml-automl-runtime**
    + U hebt het probleem opgelost met prognoses wanneer de gegevensset korte korrels bevat met lange tijds intervallen.
    + Het probleem is opgelost wanneer de automatische maximum horizon is ingeschakeld en de datum kolom bevat datums in een vorm van teken reeksen. Er is een juiste conversie en een goede fout toegevoegd als de conversie op datum niet mogelijk is
    + Systeem eigen NumPy en SciPy gebruiken voor het serialiseren en deserialiseren van tussenliggende gegevens voor file cache Store (gebruikt voor lokale AutoML-uitvoeringen)
    + Er is een fout opgelost waarbij mislukte onderliggende uitvoeringen in de actieve status kunnen raken.
  + **azureml-cli-algemeen**
    + Profileren van één exemplaar is opgelost om een aanbeveling te doen en is beschikbaar gesteld in de core-SDK.
  + **azureml-core**
    + Toegevoegd `--grant-workspace-msi-access` als een extra para meter voor de Data Store CLI voor het registreren van de Azure Blob-container, waarmee u een BLOB-container kunt registreren die zich achter een VNet bevindt.
    + Profileren van één exemplaar is opgelost om een aanbeveling te doen en is beschikbaar gesteld in de core-SDK.
    + Het probleem is opgelost in aks.py _deploy
    + Valideert de integriteit van modellen die worden geüpload om problemen met de Stille opslag te voor komen.
  + **azureml-interpret**
    + uitzonde ringen voor azureml-stijl toegevoegd aan azureml-interprete
    + vaste DeepScoringExplainer-serialisatie voor Keras-modellen
  + **azureml-pipeline-core**
    + Pipeline batch Score notebook maakt nu gebruik van ParallelRunStep
  + **azureml-pipeline-steps**
    + De `AutoMLStep` in het pakket is verplaatst `azureml-pipeline-steps` . Afgeschaft `AutoMLStep` in `azureml-train-automl-runtime` .
  + **azureml-contrib-pipeline-steps**
    + Optionele para meter side_inputs toegevoegd aan ParallelRunStep. Deze para meter kan worden gebruikt om de map in de container te koppelen. De momenteel ondersteunde typen zijn DataReference en PipelineData.
  + **azureml-tensorboard**
    + update voor azureml-tensorboard om tensor Flow 2,0 te ondersteunen
  + **azureml-train-automl-client**
    + Er is een vast FeaturizationConfig-probleem opgetreden waardoor aangepaste parametrisatie-configuratie wordt gefilterd.
  + **azureml-train-automl-runtime**
    + De `AutoMLStep` in het pakket is verplaatst `azureml-pipeline-steps` . Afgeschaft `AutoMLStep` in `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Ondersteuning voor PyTorch-versie 1,4 in de PyTorch-estimator
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK voor python v 1.1.0 RC0 (voorlopige versie)

+ **Wijzigingen die fouten veroorzaken**
  + **Semantic Versioning 2.0.0**
    + Vanaf versie 1,1 Azure ML python SDK worden semantische versie beheer 2.0.0. [Meer informatie vindt u hier](https://semver.org/). Alle volgende versies volgen een nieuw Nummerings schema en een contract voor semantische versie. 
  
+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-runtime**
    + Verhoogde snelheid van parametrisatie.
    + De frequentie controle tijdens de score is opgelost. in de prognose taken is geen strikte frequentie-equivalentie vereist tussen de trein-en testset.
  + **azureml-core**
    + De gebruiker kan nu een waarde opgeven voor de verificatie sleutel bij het opnieuw genereren van sleutels voor webservices.
  + **azureml-interpret**
    + Bijgewerkt azureml-interpreteren om afhankelijk te zijn van interprete-Community 0.5.0
  + **azureml-pipeline-core**
    + Er is een fout opgelost waarbij PythonScriptStep resultaten onjuist opnieuw kunnen worden gebruikt, ondanks het wijzigen van de lijst met argumenten
  + **azureml-pipeline-steps**
    + Voor beeld van toegevoegde documentatie voor gegevensset als PythonScriptStep-invoer
  + **azureml-contrib-pipeline-steps**
    + Para meters die in ParallelRunConfig worden door gegeven, kunnen worden overschreven door de pijplijn parameters nu door te geven. Nieuwe pijplijn parameters die worden ondersteund aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count en aml_process_count_per_node maken al deel uit van een eerdere versie).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK voor python v-1.0.85

+ **Nieuwe functies**
  + **azureml-core**
    + Het huidige kern gebruik en de quotum limiet voor AmlCompute-resources in een bepaalde werk ruimte en een abonnement ophalen
  
  + **azureml-contrib-pipeline-steps**
    + Gebruikers toestaan om tabellaire gegevensset door te geven als een tussentijds resultaat van de vorige stap naar parallelrunstep

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-runtime**
    + De vereiste van y_query kolom in de aanvraag voor de geïmplementeerde Forecasting-service is verwijderd. 
    + De y_query is verwijderd uit de sectie Dominick van de oranje sap-Notebook Service.
    + Er is een probleem opgelost met de fout bij het voor komen van prognoses op de geïmplementeerde modellen, het werken met gegevens sets met datum en tijd kolommen.
    + Er is een Matthews-correlatie coëfficiënt toegevoegd als classificatie-metriek voor zowel binaire als multi klassen classificatie.
  + **azureml-contrib-interpret**
    + Verwijderde tekst uitleg van azureml-contrib-interpreteren als tekst uitleg is verplaatst naar de opslag plaats van het interpreteren van tekst die binnenkort worden vrijgegeven.
  + **azureml-core**
    + Gegevensset: de gebruiks gegevens voor een bestands-gegevensset zijn niet meer afhankelijk van numpy en Pandas die moeten worden geïnstalleerd in de python-env.
    + LocalWebservice.wait_for_deployment () is gewijzigd om de status van de lokale docker-container te controleren voordat u probeert het status eindpunt te pingen, waardoor de hoeveelheid tijd die nodig is om een mislukte implementatie te melden, aanzienlijk wordt verminderd.
    + De initialisatie van een interne eigenschap die wordt gebruikt in LocalWebservice. Reload () is opgelost wanneer het Service object wordt gemaakt op basis van een bestaande implementatie met behulp van de LocalWebservice ()-constructor.
    + Bewerkt fout bericht voor een toelichting.
    + Er is een nieuwe methode met de naam get_access_token () toegevoegd aan AksWebservice die een AksServiceAccessToken-object retourneert dat een toegangs token bevat, vernieuwt na tijds tempel, verval datum van het tijds tempel en het token type. 
    + De bestaande methode get_token () in AksWebservice is afgeschaft, omdat alle informatie die deze methode retourneert, wordt geretourneerd.
    + Gewijzigde uitvoer van AZ ml service Get-access-token opdracht. De naam van het token is gewijzigd in accessToken en refreshBy naar refreshAfter. De eigenschappen expiryOn en Token type zijn toegevoegd.
    + Vaste get_active_runs
  + **azureml-explain-model**
    + Shap bijgewerkt naar 0.33.0 en interpreteert-community naar 0,4. *
  + **azureml-interpret**
    + Shap bijgewerkt naar 0.33.0 en interpreteert-community naar 0,4. *
  + **azureml-train-automl-runtime**
    + Er is een Matthews-correlatie coëfficiënt toegevoegd als classificatie-metriek voor zowel binaire als multi klassen classificatie.
    + Preproces-markering van code uitdrukken en vervangen door parametrisatie-parametrisatie is standaard ingeschakeld

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK voor python v-1.0.83

+ **Nieuwe functies**
  + Gegevensset: Voeg twee opties toe `on_error` en `out_of_range_datetime` voor `to_pandas_dataframe` om te mislukken wanneer gegevens fout waarden hebben in plaats van ze te vullen met `None` .
  + Werk ruimte: de `hbi_workspace` markering voor werk ruimten met gevoelige gegevens toegevoegd, waardoor verdere versleuteling mogelijk is en geavanceerde diagnoses in werk ruimten wordt uitgeschakeld. Er is ook ondersteuning toegevoegd voor het maken van uw eigen sleutels voor het bijbehorende Cosmos DB-exemplaar door de `cmk_keyvault` para meters en op te geven `resource_cmk_uri` bij het aanmaken van een werk ruimte, waardoor een Cosmos DB exemplaar in uw abonnement wordt gemaakt tijdens het inrichten van uw werk ruimte. [Meer informatie vindt u hier.](./concept-data-encryption.md#azure-cosmos-db)

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-runtime**
    + Er is een regressie opgelost waardoor een TypeError wordt geactiveerd wanneer AutoML wordt uitgevoerd op python-versies onder 3.5.4.
  + **azureml-core**
    + Er is een probleem opgelost in een `datastore.upload_files` relatief pad dat `./` niet kon worden gebruikt.
    + Er zijn afschaffing-berichten toegevoegd voor alle code paden van de afbeeldings klasse
    + Vaste Modelbeheer URL-constructie voor Azure China 21Vianet-regio.
    + Er is een probleem opgelost waarbij modellen die gebruikmaken van source_dir, niet kunnen worden verpakt voor Azure Functions.    
    + Er is een optie toegevoegd voor [environment.build_local ()](/python/api/azureml-core/azureml.core.environment.environment) om een afbeelding te pushen naar het container register voor de AzureML-werk ruimte
    + De SDK is bijgewerkt met het gebruik van nieuwe token bibliotheek op Azure Synapse op een achtergrond die daarmee compatibel is.
  + **azureml-interpret**
    + Er is een probleem opgelost waarbij geen fout is geretourneerd toen geen verklaringen kunnen worden gedownload. Genereert nu een uitzonde ring, overeenkomend gedrag elders.
  + **azureml-pipeline-steps**
    + Het door geven `DatasetConsumptionConfig` `Estimator` van de s naar de para meter is niet toegestaan `inputs` wanneer de `Estimator` wordt gebruikt in een `EstimatorStep` .
  + **azureml-SDK**
    + De AutoML-client is toegevoegd aan het azureml-SDK-pakket, waardoor externe AutoML-uitvoeringen worden verzonden zonder het volledige AutoML-pakket te installeren.
  + **azureml-train-automl-client**
    + Gecorrigeerde uitlijning van console-uitvoer voor AutoML-uitvoeringen
    + Er is een fout opgelost waarbij een onjuiste versie van Pandas kan worden geïnstalleerd op de externe amlcompute.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK voor python v-1.0.81

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-contrib-interpret**
    + Shap-afhankelijkheid uitstellen om te interpreteren-community van azureml-interpret
  + **azureml-core**
    + Compute target kan nu worden opgegeven als een para meter voor de bijbehorende Deployment config-objecten. Dit is in het bijzonder de naam van het reken doel om te implementeren, niet voor het SDK-object.
    + De CreatedBy-informatie is toegevoegd aan model-en service-objecten. Is mogelijk toegankelijk through.created_by
    + Fixed ContainerImage. run (), die de HTTP-poort van de docker-container niet juist heeft ingesteld.
    + `azureml-dataprep`Optioneel maken voor `az ml dataset register` cli-opdracht
    + Er is een fout opgelost waarbij een probleem `TabularDataset.to_pandas_dataframe` niet meer wordt weer gegeven in een alternatieve lezer en een waarschuwing wordt afgedrukt.
  + **azureml-explain-model**
    + Shap-afhankelijkheid uitstellen om te interpreteren-community van azureml-interpret
  + **azureml-pipeline-core**
    + Nieuwe stap voor de pijp lijn toegevoegd `NotebookRunnerStep` voor het uitvoeren van een lokaal notitie blok als een stap in de pijp lijn.
    + Afgeschafte get_all functies verwijderd voor PublishedPipelines, planningen en PipelineEndpoints
  + **azureml-train-automl-client**
    + De afschaffing van data_script is gestart als invoer voor AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK voor python v-1.0.79

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + FeaturizationConfig verwijderd om te worden geregistreerd
      + De logboek registratie is bijgewerkt zodat alleen ' auto '/' uit '/' is aangepast '.
  + **azureml-automl-runtime**
    + Er is ondersteuning toegevoegd voor Pandas. Reeksen en Pandas. Categorische voor het detecteren van kolom gegevens type. Voorheen alleen ondersteund numpy. ndarray
      + Er zijn gerelateerde code wijzigingen toegevoegd aan de categorische-dtype op de juiste manier.
    + De functie-interface voor de Forecaster is verbeterd: de para meter y_pred is optioneel gemaakt. -De docstrings zijn verbeterd.
  + **azureml-contrib-dataset**
    + Er is een fout opgelost waarbij het labelen van gegevens sets niet kan worden gekoppeld.
  + **azureml-core**
    + Fout oplossing voor `Environment.from_existing_conda_environment(name, conda_environment_name)` . Gebruiker kan een instantie van een omgeving maken die een exacte replica is van de lokale omgeving
    + De standaard waarden voor de tijd reeks gerelateerde gegevens sets zijn gewijzigd `include_boundary=True` .
  + **azureml-train-automl-client**
    + Er is een probleem opgelost waarbij de validatie resultaten niet worden afgedrukt wanneer de uitvoer weer geven is ingesteld op ONWAAR.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK voor python v-1.0.76

+ **Wijzigingen die fouten veroorzaken**
  + Upgrade problemen voor Azureml-Train-AutoML
    + Upgraden naar azureml-Train-automl>= 1.0.76 van azureml-Train-automl<1.0.76 kan gedeeltelijke installaties veroorzaken, waardoor sommige AutoML-import bewerkingen mislukken. U kunt dit oplossen door het installatie script uit te voeren dat is gevonden op https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd . Of als u PIP direct gebruikt, kunt u het volgende doen:
      + "PIP-installatie--upgrade voor azureml-Train-automl"
      + "PIP-installatie--ignore-installed azureml-Train-automl-client"
    + of u kunt de oude versie verwijderen voordat u een upgrade uitvoert
      + "PIP verwijderen azureml-Train-automl"
      + "PIP-installatie van azureml-Train-automl"

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-runtime**
    + Voor AutoML wordt nu rekening gehouden met de klassen True en False bij het berekenen van de gemiddelde scalaire metrische gegevens voor binaire classificatie taken.
    + Geplaatste machine learning-en trainings code in AzureML-AutoML-core naar een nieuw pakket AzureML-AutoML-runtime.
  + **azureml-contrib-dataset**
    + Wanneer `to_pandas_dataframe` u een gegevensset met een label met de optie downloaden aanroept, kunt u nu opgeven of bestaande bestanden moeten worden overschreven.
    + Bij het aanroepen `keep_columns` of `drop_columns` dat resulteert in een time series-, label-of image-kolom die wordt neergezet, worden de bijbehorende mogelijkheden ook voor de gegevensset verwijderd.
    + Er is een probleem opgelost met het laad programma pytorch voor de object detectie taak.
  + **azureml-contrib-interpret**
    + De object beschrijving van het uitleg Dashboard van het azureml-contrib-interpreteren, gewijzigde pakket dat verwijst naar het nieuwe item in interpret_community
    + Bijgewerkte versie van interprete-community naar 0.2.0
  + **azureml-core**
    + Verbeter de prestaties van `workspace.datasets` .
    + De mogelijkheid om Azure SQL Database gegevens opslag te registreren met behulp van gebruikers naam-en wachtwoord verificatie is toegevoegd
    + Oplossing voor het laden van RunConfigurations vanaf relatieve paden.
    + Bij het aanroepen `keep_columns` of `drop_columns` dat resulteert in een time series-kolom die wordt verwijderd, worden ook de bijbehorende mogelijkheden voor de gegevensset verwijderd.
  + **azureml-interpret**
    + bijgewerkte versie van interprete-community naar 0.2.0
  + **azureml-pipeline-steps**
    + Gedocumenteerde ondersteunde waarden voor de `runconfig_pipeline_params` stappen van Azure machine learning-pijp lijn.
  + **azureml-pipeline-core**
    + De CLI-optie is toegevoegd om de uitvoer in JSON-indeling voor pijplijn opdrachten te downloaden.
  + **azureml-Train-automl**
    + Gesplitste-Train-AutoML in twee pakketten, een client pakket AzureML-Train-AutoML-client en een ML-trainings pakket AzureML-Train-AutoML-runtime
  + **azureml-train-automl-client**
    + Er is een thin client toegevoegd voor het verzenden van AutoML experimenten zonder dat machine learning afhankelijkheden lokaal moeten worden geïnstalleerd.
    + Vaste logboek registratie van automatisch gedetecteerde lags, grootte van het rollend venster en maximum aantal Horizons in de externe uitvoeringen.
  + **azureml-train-automl-runtime**
    + Er is een nieuw AutoML-pakket toegevoegd om machine learning-en runtime-onderdelen van de client te isoleren.
  + **azureml-contrib-Train-RL**
    + Extra ondersteuning voor het leren van de versterking in SDK.
    + Er is ondersteuning toegevoegd voor AmlWindowsCompute in de RL-SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK voor python v-1.0.74

  + **Preview-functies**
    + **azureml-contrib-dataset**
      + Nadat u azureml-contrib-dataset hebt geïmporteerd, kunt u `Dataset.Labeled.from_json_lines` in plaats van `._Labeled` een gegevensset met een label maken een aanroep uit te voeren.
      + Wanneer `to_pandas_dataframe` u een gegevensset met een label met de optie downloaden aanroept, kunt u nu opgeven of bestaande bestanden moeten worden overschreven.
      + Bij het aanroepen `keep_columns` of `drop_columns` dat resulteert in een time series-, label-of image-kolom die wordt neergezet, worden de bijbehorende mogelijkheden ook voor de gegevensset verwijderd.
      + Opgeloste problemen met PyTorch loader bij het aanroepen `dataset.to_torchvision()` .

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Model Profiler toegevoegd aan de preview-versie van de CLI.
    + Opgeloste wijzigingen in Azure Storage waardoor de AzureML CLI mislukt.
    + Load Balancer type is toegevoegd aan MLC voor AKS-typen
  + **azureml-automl-core**
    + Er is een probleem opgelost met de detectie van een maximum periode van een bepaalde periode, met ontbrekende waarden en meerdere korrels.
    + Het probleem is opgelost doordat er fouten zijn opgetreden tijdens het genereren van splitsingen tussen meerdere validaties.
    + Vervang deze sectie door een bericht met een kortings indeling om te worden weer gegeven in de release opmerkingen:-verbeterde verwerking van korte korrels in de prognose van gegevens sets.
    + Het probleem met maskering van sommige gebruikers gegevens tijdens de logboek registratie is opgelost. -Verbeterde logboek registratie van de fouten tijdens de uitvoering van prognoses.
    + Psutil toevoegen als een Conda-afhankelijkheid voor het automatisch gegenereerde yml-implementatie bestand.
  + **azureml-contrib-Mir**
    + Opgeloste wijzigingen in Azure Storage waardoor de AzureML CLI mislukt.
  + **azureml-core**
    + Hiermee wordt een bug opgelost die de oorzaak is van modellen die zijn geïmplementeerd op Azure Functions om 500 te produceren.
    + Er is een probleem opgelost waarbij het amlignore-bestand niet op moment opnamen werd toegepast.
    + Er is een nieuwe API toegevoegd amlcompute.get_active_runs die een generator retourneert voor het uitvoeren en in de wachtrij plaatsen van een opgegeven amlcompute.
    + Load Balancer type is toegevoegd aan MLC voor AKS-typen.
    + Append_prefix BOOL-para meter is toegevoegd aan download_files in run.py en download_artifacts_from_prefix in artifacts_client. Deze markering wordt gebruikt om het oorspronkelijke bestandspad op te platen zodat alleen de naam van het bestand of de map wordt toegevoegd aan de output_directory
    + Los het deserialisatie probleem op voor het `run_config.yml` gebruik van de gegevensset.
    + Bij het aanroepen `keep_columns` of `drop_columns` dat resulteert in een time series-kolom die wordt verwijderd, worden ook de bijbehorende mogelijkheden voor de gegevensset verwijderd.
  + **azureml-interpret**
    + Bijgewerkte interpretatie-Community-versie naar 0.1.0.3
  + **azureml-Train-automl**
    + Er is een probleem opgelost waarbij automl_step mogelijk geen validatie problemen afdrukt.
    + Er is een vaste register_model, zelfs als voor de omgeving van het model lokaal afhankelijkheden ontbreken.
    + Er is een probleem opgelost waarbij op externe uitvoeringen geen docker is ingeschakeld.
    + Voeg logboek registratie toe van de uitzonde ring die ervoor zorgt dat een lokale uitvoering voor tijdig mislukt.
  + **azureml-train-core**
    + U kunt resume_from uitvoeren in de berekening van de best onderliggende uitvoeringen van automatische afstemming-afstemming.
  + **azureml-pipeline-core**
    + Vaste parameter verwerking in pijplijn argument constructie.
    + De pijplijn beschrijving en de yaml-para meter voor het stap type zijn toegevoegd.
    + Nieuwe YAML-indeling voor pijplijn stap en toegevoegde waarschuwing voor afschaffing van oude indeling.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webervaring

De landings pagina van de werk ruimte voor samen werking op [https://ml.azure.com](https://ml.azure.com) is verbeterd en is opnieuw ingesteld als Azure machine learning Studio.

Vanuit de Studio kunt u Azure Machine Learning assets trainen, testen, implementeren en beheren, zoals gegevens sets, pijp lijnen, modellen, eind punten en meer.

Toegang tot de volgende webgebaseerde hulp middelen voor ontwerpen vanuit Studio:

| Webgebaseerd hulp programma | Beschrijving | 
|-|-|-|
| VM van notebook (preview-versie) | Volledig beheerd werk station in de Cloud | 
| [Automatische machine learning](tutorial-first-experiment-automated-ml.md) (preview-versie) | Geen code-ervaring voor het automatiseren van de ontwikkeling van machine learning modellen | 
| [Designer](concept-designer.md) | Hulp programma voor het machine learning modellen model slepen en neerzetten voorheen bekend als de visuele interface | 


### <a name="azure-machine-learning-designer-enhancements"></a>Verbeteringen in Azure Machine Learning Designer

+ Voorheen bekend als de visuele interface 
+    11 nieuwe [modules](algorithm-module-reference/module-reference.md) , waaronder aanbevolen procedures, classificaties en trainings hulpprogramma's, waaronder functie techniek, kruis validatie en gegevens transformatie.

### <a name="r-sdk"></a>R SDK 
 
Gegevens wetenschappers en AI-ontwikkel aars gebruiken de [Azure machine learning SDK voor R](https://github.com/Azure/azureml-sdk-for-r) om machine learning werk stromen met Azure machine learning te bouwen en uit te voeren.

De Azure Machine Learning SDK voor R gebruikt het `reticulate` pakket om een verbinding te maken met de python-SDK. Door rechtstreeks aan python te binden, kunt u met de SDK voor R toegang krijgen tot kern objecten en methoden die zijn geïmplementeerd in de python-SDK vanuit elke R-omgeving die u kiest.

De belangrijkste mogelijkheden van de SDK zijn:

+    Cloudresources voor het bewaken, loggen en organiseren van uw machine-leerexperimenten beheren.
+    Train modellen met behulp van cloud resources, waaronder GPU-versneld model training.
+    Implementeer uw modellen als webservices op Azure Container Instances (ACI) en Azure Kubernetes service (AKS).

Raadpleeg de [pakket website](https://azure.github.io/azureml-sdk-for-r) voor volledige documentatie.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Integratie met Event Grid Azure Machine Learning 

Azure Machine Learning is nu een resource provider voor Event Grid, kunt u machine learning gebeurtenissen configureren via de Azure Portal of Azure CLI. Gebruikers kunnen gebeurtenissen maken voor het volt ooien van de uitvoering, model registratie, model implementatie en gegevens drift gedetecteerd. Deze gebeurtenissen kunnen worden doorgestuurd naar gebeurtenis-handlers die worden ondersteund door Event Grid voor verbruik. Zie machine learning gebeurtenis [schema](../event-grid/event-schema-machine-learning.md) en [zelf studie](how-to-use-event-grid.md) artikelen voor meer informatie.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK voor python v-1.0.72

+ **Nieuwe functies**
  + Er zijn gegevensset-monitors toegevoegd via het [**azureml-datadrift-**](/python/api/azureml-datadrift) pakket, waardoor het mogelijk is om gegevens reeksen van tijd Series te bewaken voor gegevensdrift of andere statistische wijzigingen gedurende een bepaalde periode. Waarschuwingen en gebeurtenissen kunnen worden geactiveerd als er een drift wordt gedetecteerd of als aan andere voor waarden voor de gegevens wordt voldaan. Zie [onze documentatie](how-to-monitor-datasets.md) voor meer informatie.
  + Het aankondigen van twee nieuwe edities (ook wel een andere SKU genoemd) in Azure Machine Learning. Met deze versie kunt u nu een Basic-of ENTER prise Azure Machine Learning-werk ruimte maken. Alle bestaande werk ruimten worden standaard in de Basic-editie gezet en u kunt naar de Azure Portal of naar de Studio gaan om de werk ruimte op elk gewenst moment bij te werken. U kunt een Basic-of ENTER prise-werk ruimte maken op basis van de Azure Portal. Lees [onze documentatie](./how-to-manage-workspace.md) voor meer informatie. In de SDK kunt u de editie van uw werk ruimte bepalen met behulp van de eigenschap SKU van uw werkruimte object.
  + We hebben ook verbeteringen aangebracht in Azure Machine Learning Compute: u kunt nu metrische gegevens weer geven voor uw clusters (zoals het totale aantal knoop punten, actieve knoop punten, het totale kern quotum) in Azure Monitor, naast het weer geven van Diagnostische logboeken voor fout opsporing. Daarnaast kunt u nu actieve of in de wachtrij geplaatste uitvoeringen weer geven op uw cluster en Details, zoals de IP-adressen van de verschillende knoop punten in uw cluster. U kunt deze weer geven in de portal of met behulp van de bijbehorende functies in de SDK of CLI.

  + **Preview-functies**
    + We geven preview-ondersteuning uit voor schijf versleuteling van uw lokale SSD in Azure Machine Learning compute. Verhoog het technische ondersteunings ticket om uw abonnement in staat te stellen deze functie te gebruiken.
    + Open bare preview van Azure Machine Learning batch deinterferentie. Azure Machine Learning batch-demijner doelen verkleint grote interferentie taken die niet tijd gevoelig zijn. Batch-demijnen bieden rendabele berekenings berekeningen, met een niet-parallelle door Voer voor asynchrone toepassingen. De oplossing is geoptimaliseerd voor een hoge door Voer, brand-en-vergeet om grote verzamelingen gegevens te deactiveren.
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
        + Ingeschakelde functionaliteiten voor gegevensset met labels
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + CLI ondersteunt nu model pakketten.
    + De gegevensset-CLI is toegevoegd. Meer informatie: `az ml dataset --help`
    + Er is ondersteuning toegevoegd voor het implementeren en verpakken van ondersteunde modellen (ONNX, scikit-Learn en tensor flow) zonder een InferenceConfig-exemplaar.
    + Overschrijvings vlag toegevoegd voor service-Implementatie (ACI en AKS) in SDK en CLI. Als dit wordt gegeven, wordt de bestaande service overschreven als er al een service met de naam bestaat. Als de service niet bestaat, wordt er een nieuwe service gemaakt.
    + Modellen kunnen worden geregistreerd met twee nieuwe frameworks, Onnx en tensor flow. -Model registratie accepteert voorbeeld invoer gegevens, voorbeeld uitvoer gegevens en resource configuratie voor het model.
  + **azureml-automl-core**
    + Training een herhaling wordt alleen uitgevoerd in een onderliggend proces als er runtime-beperkingen worden ingesteld.
    + Er is een guardrail toegevoegd voor prognose taken om te controleren of een opgegeven max_horizon een geheugen probleem op de desbetreffende computer veroorzaakt. Als dat het geval is, wordt er een guardrail-bericht weer gegeven.
    + Toegevoegde ondersteuning voor complexe frequenties, zoals twee jaar en één maand. -Er is een begrijpelijk fout bericht toegevoegd als de frequentie niet kan worden bepaald.
    + Voeg de standaard waarden van azureml toe aan automatisch gegenereerde Conda env om het model implementatie fout op te lossen
    + Toestaan dat tussenliggende gegevens in Azure Machine Learning pijp lijn worden geconverteerd naar de tabellaire gegevensset en worden gebruikt in `AutoMLStep` .
    + Geïmplementeerde update van het kolom doel voor streaming.
    + De para meter update voor de trans formatie is geïmplementeerd voor de toerekenings-en HashOneHotEncoder voor streaming.
    + De huidige gegevens grootte en de mini maal vereiste gegevens grootte zijn toegevoegd aan de validatie fout berichten.
    + De minimale vereiste gegevens grootte voor kruis validatie is bijgewerkt om mini maal twee steek proeven te garanderen in elke validatie vouw.
  + **azureml-cli-algemeen**
    + CLI ondersteunt nu model pakketten.
    + Modellen kunnen worden geregistreerd met twee nieuwe frameworks, Onnx en tensor flow.
    + Model registratie accepteert voorbeeld invoer gegevens, voorbeeld uitvoer gegevens en resource configuratie voor het model.
  + **azureml-contrib-GBDT**
    + het release kanaal voor het notitie blok is opgelost
    + Er is een waarschuwing toegevoegd voor een niet-AmlCompute Compute-doel dat niet wordt ondersteund
    + LightGMB Estimator toegevoegd aan het contrib-GBDT-pakket
  + [**azureml-core**](/python/api/azureml-core)
    + CLI ondersteunt nu model pakketten.
    + Waarschuwing voor afschaffing toevoegen voor afgeschafte gegevensset-Api's. Zie de opmerking over de DataSet API-wijziging op https://aka.ms/tabular-dataset .
    + Wijzig [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) de registratie naam en-versie als de gegevensset is geregistreerd.
    + Een bug oplossen die ScriptRunConfig met dataset as-argument niet herhaaldelijk kan worden gebruikt om de uitvoering van het experiment te verzenden.
    + Gegevens sets die zijn opgehaald tijdens een uitvoering, worden bijgehouden en kunnen worden weer gegeven op de pagina Details van uitvoering of door aanroepen [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) nadat de uitvoering is voltooid.
    + Toestaan dat tussenliggende gegevens in Azure Machine Learning pijp lijn worden geconverteerd naar de tabellaire gegevensset en worden gebruikt in [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) .
    + Er is ondersteuning toegevoegd voor het implementeren en verpakken van ondersteunde modellen (ONNX, scikit-Learn en tensor flow) zonder een InferenceConfig-exemplaar.
    + Overschrijvings vlag toegevoegd voor service-Implementatie (ACI en AKS) in SDK en CLI. Als dit wordt gegeven, wordt de bestaande service overschreven als er al een service met de naam bestaat. Als de service niet bestaat, wordt er een nieuwe service gemaakt.
    +  Modellen kunnen worden geregistreerd met twee nieuwe frameworks, Onnx en tensor flow. Model registratie accepteert voorbeeld invoer gegevens, voorbeeld uitvoer gegevens en resource configuratie voor het model.
    + Het nieuwe gegevens Archief voor de Azure Database for MySQL is toegevoegd. Er is een voor beeld toegevoegd voor het gebruik van Azure Database for MySQL in DataTransferStep in Azure Machine Learning pijp lijnen.
    + Functionaliteit toegevoegd om tags toe te voegen aan of te verwijderen uit de functionaliteit voor het toevoegen van experimenten om tags uit uitvoeringen te verwijderen
    + Overschrijvings vlag toegevoegd voor service-Implementatie (ACI en AKS) in SDK en CLI. Als dit wordt gegeven, wordt de bestaande service overschreven als er al een service met de naam bestaat. Als de service niet bestaat, wordt er een nieuwe service gemaakt.
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + Verplaatst van `azureml-contrib-datadrift` naar `azureml-datadrift`
    + Er is ondersteuning toegevoegd voor het bewaken van Time Series-gegevens sets voor drift en andere statistische metingen
    + Nieuwe methoden `create_from_model()` en `create_from_dataset()` voor de [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) klasse. De `create()` methode wordt afgeschaft.
    + Aanpassingen van de visualisaties in Python en gebruikers interface in de Azure Machine Learning Studio.
    + Ondersteuning voor wekelijkse en maandelijkse monitor planning, naast dagelijks voor gegevensset-monitors.
    + Ondersteuning voor backfill voor het analyseren van historische gegevens voor gegevensset-monitors.
    + Verschillende oplossingen voor oplossingen
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + azureml-dataprep is niet meer nodig om een Azure Machine Learning pijp lijn uit te voeren vanuit het pijplijn `yaml` bestand.
  + [**azureml-Train-automl**](/python/api/azureml-train-automl-runtime/)
    + Voeg de standaard waarden van azureml toe aan automatisch gegenereerde Conda env om het model implementatie fout op te lossen
    + AutoML remote training bevat nu de standaard waarden van azureml om hergebruik van training env voor te stellen.
  + **azureml-train-core**
    + Ondersteuning voor PyTorch 1,3 toegevoegd in [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch) estimator

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Visuele interface (preview-versie)

+ De Azure Machine Learning Visual Interface (preview) is ingezien om te worden uitgevoerd op [Azure machine learning pijp lijnen](concept-ml-pipelines.md). Pijp lijnen (voorheen bekend als experimenten) die zijn gemaakt in de visuele interface, zijn nu volledig geïntegreerd met de kern Azure Machine Learning-ervaring.
  + Unified management-ervaring met SDK-assets
  + Versie beheer en het bijhouden van visuele interface modellen, pijp lijnen en eind punten
  + Nieuwe gebruikers interface
  + Implementatie voor batch-demijnen is toegevoegd
  + De ondersteuning voor Azure Kubernetes service (AKS) is toegevoegd voor de doel stellingen voor de inschiet kracht
  + Nieuwe werk stroom voor het ontwerpen van een python-stap
  + Nieuwe [landings pagina](https://ml.azure.com) voor visuele hulpprogram ma's voor ontwerpen

+ **Nieuwe modules**
  + Wiskundige bewerking Toep assen
  + SQL-trans formatie Toep assen
  + Clip waarden
  + Gegevens samenvatten
  + Importeren uit SQL Database

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK voor python v-1.0.69

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + Het beperken van de model verklaringen voor de beste uitvoering in plaats van het berekenen van de uitleg voor elke uitvoering. U kunt dit gedrag wijzigen voor lokale, externe en ADB.
    + Er is ondersteuning toegevoegd voor de uitleg van het on-demand-model voor de gebruikers interface
    + Psutil is toegevoegd als een afhankelijkheid van `automl` en opgenomen psutil als een Conda-afhankelijkheid in amlcompute.
    + Het probleem met heuristische lags-en Rolling venster grootten is opgelost in de Voorspellings gegevens sets waarvan sommige reeksen een lineaire algebra fouten kunnen veroorzaken
      + Er is afdrukken toegevoegd voor de heuristisch verbepaald para meters in de prognoses worden uitgevoerd.
  + **azureml-contrib-datadrift**
    + Er is beveiliging toegevoegd tijdens het maken van metrische gegevens voor uitvoer als DataSet level drift zich niet in de eerste sectie bevindt.
  + **azureml-contrib-interpret**
    + de naam van het pakket voor azureml-contrib-uitleg-model is gewijzigd in azureml-contrib-interpreteren
  + **azureml-core**
    + API is toegevoegd om de registratie van gegevens sets op te heffen. `dataset.unregister_all_versions()`
    + de naam van het pakket voor azureml-contrib-uitleg-model is gewijzigd in azureml-contrib-interpreteren.
  + **[azureml-core](/python/api/azureml-core)**
    + API is toegevoegd om de registratie van gegevens sets op te heffen. sets. [unregister_all_versions ()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + De gegevensset-API is toegevoegd om de gewijzigde tijd van de gegevens te controleren. `dataset.data_changed_time`.
    + `FileDataset`U kunt en `TabularDataset` als invoer voor `PythonScriptStep` , `EstimatorStep` en `HyperDriveStep` in azure machine learning pijp lijn gebruiken
    + Prestaties van zijn `FileDataset.mount` verbeterd voor mappen met een groot aantal bestanden
    + [FileDataset](/python/api/azureml-core/azureml.data.filedataset) en [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) kunnen als invoer worden gebruikt voor [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)en [HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) in de Azure machine learning-pijp lijn.
    + Prestaties van FileDataset. [Mount ()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) is verbeterd voor mappen met een groot aantal bestanden
    + De URL is toegevoegd aan bekende fout aanbevelingen in de details van de uitvoering.
    + Er is een fout opgelost in run.get_metrics waar aanvragen zouden mislukken als een uitvoering te veel onderliggende items heeft
    + Er is een fout opgelost in [Run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) waar aanvragen zouden mislukken als een uitvoering te veel onderliggende items heeft
    + Er is ondersteuning toegevoegd voor verificatie op het Arcadia-cluster.
    + Als u een experiment object maakt, wordt het experiment in de Azure Machine Learning-werk ruimte gemaakt, zodat het bijhouden van de uitvoerings geschiedenis kan worden uitgevoerd. Het experiment-ID en de gearchiveerde tijd worden gevuld in het object experiment bij het maken. Voor beeld: experiment = experiment (werk ruimte, "nieuw experiment") experiment_id = experiment.id Archive () en reactivate () zijn functies die kunnen worden aangeroepen voor een experiment om het experiment te verbergen en te herstellen, zodat het niet wordt weer gegeven in de UX of standaard wordt geretourneerd in een aanroep naar lijst experimenten. Als een nieuw experiment wordt gemaakt met dezelfde naam als een gearchiveerd experiment, kunt u de naam van het gearchiveerde experiment wijzigen door een nieuwe naam door te geven. Er kan slechts één actief experiment met een bepaalde naam zijn. Voor beeld: experiment1 = experiment (werk ruimte, "actief experiment") experiment1. Archive () # nieuw actief experiment maken met dezelfde naam als de gearchiveerde. experiment2. = Experiment (werk ruimte, "actief experiment") experiment1. reactivate (new_name = "vorig actief experiment") de lijst met statische methoden () op het experiment kan een naam filter en een View type-filter hebben. View type-waarden zijn "ACTIVE_ONLY", "ARCHIVED_ONLY" en "alle" voor beeld: archived_experiments = experiment. List (workspace, view_type = "ARCHIVED_ONLY") all_first_experiments = experiment. List (werk ruimte, naam = "eerste experiment", view_type = "ALL")
    + Ondersteuning voor het gebruik van een omgeving voor model implementatie en service-update
  + **azureml-datadrift**
    + Het kenmerk show van de klasse DataDriftDector biedt geen ondersteuning voor het optionele argument with_details meer. Bij het kenmerk show wordt alleen de bijdrage van de functie kolommen gegevens drift en gegevens drift weer gegeven.
    + Gedrag van het DataDriftDetector-kenmerk ' get_output ' wordt gewijzigd:
      + Invoer parameter start_time, end_time optioneel zijn in plaats van verplicht.
      + De invoer van specifieke start_time en/of end_time met een specifieke run_id in dezelfde aanroepen leidt ertoe dat er een fout uitzonde ring optreedt omdat deze elkaar wederzijds uitsluiten
      + Als er specifieke start_time en/of end_time worden opgegeven, worden alleen de resultaten van geplande uitvoeringen geretourneerd.
      + De para meter daily_latest_only is afgeschaft.
    + Ondersteuning bij het ophalen van op gegevensset gebaseerde gegevens-drift-uitvoer.
  + **azureml-explain-model**
    + De naam van het pakket voor AzureML-uitleggen-model wordt gewijzigd in AzureML-interpreteren, waarbij het oude pakket wordt gehandhaafd voor achterwaartse compatibiliteit voor nu
    + Er is een opgeloste `automl` bug met onbewerkte uitleg ingesteld op de classificatie taak in plaats van regressie standaard bij het downloaden van ExplanationClient
    + Voeg ondersteuning toe voor `ScoringExplainer` om direct te worden gemaakt met `MimicWrapper`
  + **azureml-pipeline-core**
    + Verbeterde prestaties voor het maken van een grote pijp lijn
  + **azureml-train-core**
    + Tensor Flow 2,0-ondersteuning is toegevoegd in tensor flow estimator
  + **azureml-Train-automl**
    + Als u een [experiment](/python/api/azureml-core/azureml.core.experiment.experiment) object maakt, wordt het experiment in de Azure machine learning-werk ruimte gemaakt, zodat het bijhouden van de uitvoerings geschiedenis kan worden uitgevoerd. Het experiment-ID en de gearchiveerde tijd worden gevuld in het object experiment bij het maken. Voorbeeld:

        ```python
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [Archive ()](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) en [reactivate ()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) zijn functies die kunnen worden aangeroepen voor een experiment om het experiment te verbergen en te herstellen, zodat het niet wordt weer gegeven in de UX of standaard wordt geretourneerd in een aanroep naar lijst experimenten. Als een nieuw experiment wordt gemaakt met dezelfde naam als een gearchiveerd experiment, kunt u de naam van het gearchiveerde experiment wijzigen door een nieuwe naam door te geven. Er kan slechts één actief experiment met een bepaalde naam zijn. Voorbeeld:

        ```python
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        De [lijst met statische methoden ()](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) in experiment kan een naam filter en een View type-filter hebben. View type-waarden zijn ' ACTIVE_ONLY ', ' ARCHIVED_ONLY ' en ' ALL '. Voorbeeld:

        ```python
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Ondersteuning voor het gebruik van een omgeving voor model implementatie en service-update.
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + Het kenmerk show van de klasse [DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) biedt geen ondersteuning voor het optionele argument with_details meer. Bij het kenmerk show wordt alleen de bijdrage van de functie kolommen gegevens drift en gegevens drift weer gegeven.
    + De functie DataDriftDetector [get_output] python/API/azureml-datadrift/azureml. datadrift. DataDriftDetector. DataDriftDetector # Get-output-start-time-none--end-time-none--run-id-none-) wijzigingen:
      + Invoer parameter start_time, end_time optioneel zijn in plaats van verplicht.
      + De invoer van specifieke start_time en/of end_time met een specifieke run_id in hetzelfde aanroepen leidt ertoe dat er een fout uitzonde ring optreedt omdat deze elkaar wederzijds uitsluiten.
      + Als er specifieke start_time en/of end_time worden opgegeven, worden alleen de resultaten van geplande uitvoeringen geretourneerd.
      + De para meter daily_latest_only is afgeschaft.
    + Ondersteuning bij het ophalen van op gegevensset gebaseerde gegevens-drift-uitvoer.
  + **azureml-explain-model**
    + Voeg ondersteuning toe voor [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer) die rechtstreeks worden gemaakt met behulp van MimicWrapper
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Verbeterde prestaties voor het maken van grote pijp lijnen.
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + Tensor Flow 2,0-ondersteuning is toegevoegd in [tensor flow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + De bovenliggende uitvoering wordt niet meer uitgevoerd wanneer de installatie herhaling is mislukt, omdat deze al wordt gebruikt door de indeling.
    + Lokale-docker-en Local-Conda-ondersteuning toegevoegd voor AutoML-experimenten
    + Lokale-docker-en Local-Conda-ondersteuning toegevoegd voor AutoML experimenten.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nieuwe webervaring (preview-versie) voor Azure Machine Learning-werk ruimten

Het tabblad experiment in de [nieuwe werkruimte Portal](https://ml.azure.com) is bijgewerkt, zodat gegevens wetenschappers op een effectievere manier experimenten kunnen bewaken. U kunt de volgende functies verkennen:
+ Experimenteer met de meta gegevens om uw lijst met experimenten eenvoudig te filteren en te sorteren
+ Vereenvoudigde en uitvoerende Details pagina's voor het experiment waarmee u uw uitvoeringen kunt visualiseren en vergelijken
+ Nieuw ontwerp voor het uitvoeren van informatie pagina's om uw trainings uitvoeringen te begrijpen en te controleren

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK voor python v-1.0.65

  + **Nieuwe functies**
    + Er zijn gecuratorde omgevingen toegevoegd. Deze omgevingen zijn vooraf geconfigureerd met bibliotheken voor algemene machine learning taken en zijn vooraf gemaakt en in cache geplaatst als docker-installatie kopieën voor een snellere uitvoering. Ze worden standaard weer gegeven in de lijst met omgevingen van de werk ruimte, met het voor voegsel ' AzureML '.
    + Er zijn gecuratorde omgevingen toegevoegd. Deze omgevingen zijn vooraf geconfigureerd met bibliotheken voor algemene machine learning taken en zijn vooraf gemaakt en in cache geplaatst als docker-installatie kopieën voor een snellere uitvoering. Ze worden standaard weer gegeven in de lijst met omgevingen van de [werk ruimte](/python/api/azureml-core/azureml.core.workspace%28class%29), met het voor voegsel ' AzureML '.

  + **azureml-Train-automl**
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + De ondersteuning voor ONNX-conversie is toegevoegd voor de ADB en HDI

+ **Preview-functies**
  + **azureml-Train-automl**
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + Ondersteunde BERT en BiLSTM als tekst featurizer (alleen preview-versie)
    + Ondersteunde parametrisatie aanpassing voor de para meters voor het kolom doel en de transformator (alleen preview-versie)
    + Ondersteunde onbewerkte uitleg wanneer de gebruiker tijdens de training model uitleg maakt (alleen preview-versie)
    + Prophet voor `timeseries` prognose toegevoegd als een trained pipeline (alleen preview-versie)

  + **azureml-contrib-datadrift**
    + Pakketten die zijn verplaatst van azureml-contrib-datadrift naar azureml-datadrift; het `contrib` pakket wordt in een toekomstige versie verwijderd

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + FeaturizationConfig geïntroduceerd op AutoMLConfig en AutoMLBaseSettings
    + FeaturizationConfig geïntroduceerd op [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) en AutoMLBaseSettings
      + Kolom doel negeren voor parametrisatie met de opgegeven kolom en het functie type
      + Para meters voor transformator overschrijven
    + Er is een afschaffing bericht toegevoegd voor explain_model () en retrieve_model_explanations ()
    + Prophet toegevoegd als een trained-pijp lijn (alleen preview-versie)
    + Er is een afschaffing bericht toegevoegd voor explain_model () en retrieve_model_explanations ().
    + Prophet toegevoegd als een trained-pijp lijn (alleen preview-versie).
    + Er is ondersteuning toegevoegd voor automatische detectie van de doel-lags, de grootte van het rollen venster en de maximale waarde. Als een van target_lags, target_rolling_window_size of max_horizon is ingesteld op ' auto ', worden de heuristiek toegepast om de waarde van de overeenkomstige para meter te schatten op basis van de trainings gegevens.
    + Als er sprake is van een vaste prognose in het geval wanneer gegevensset één kolom korrel bevat, is deze korrel van een numeriek type en is er een hiaat tussen de trein-en testset
    + Het fout bericht over de gedupliceerde index in de externe uitvoering in prognose taken is opgelost
    + Als er sprake is van een vaste prognose in het geval dat de gegevensset één kolom bevat, is deze korrel van een numeriek type en is er een hiaat tussen de trein-en testset.
    + Het fout bericht over de gedupliceerde index in de externe uitvoering in prognose taken is opgelost.
    + Er is een guardrail toegevoegd om te controleren of een gegevensset niet is gebalanceerd of niet. Als dat het geval is, wordt er een guardrail-bericht naar de-console geschreven.
  + **azureml-core**
    + De mogelijkheid om SAS-URL op te halen in het model-object wordt toegevoegd aan de opslag. Bijvoorbeeld: model.get_sas_url ()
    + Introduceren `run.get_details()['datasets']` om gegevens sets op te halen die zijn gekoppeld aan de ingediende uitvoering
    + Voeg API toe `Dataset.Tabular.from_json_lines_files` om een TabularDataset te maken op basis van JSON-lijn bestanden. Ga naar [dit artikel](how-to-create-register-datasets.md) voor documentatie voor meer informatie over deze tabellaire gegevens in JSON-regel bestanden op TabularDataset.
    + Er zijn extra VM-grootte velden (besturingssysteem schijf, aantal Gpu's) toegevoegd aan de functie supported_vmsizes ()
    + Er zijn extra velden toegevoegd aan de functie list_nodes () om de uitvoering, het persoonlijke en het open bare IP-adres, de poort, enzovoort weer te geven.
    + De mogelijkheid om tijdens het inrichten van het cluster een nieuw veld op te geven: remotelogin_port_public_access dat kan worden ingesteld op ingeschakeld of uitgeschakeld, afhankelijk van of u de SSH-poort open of gesloten wilt laten op het moment van het maken van het cluster. Als u deze niet opgeeft, wordt de poort slim geopend of gesloten, afhankelijk van het feit of u het cluster in een VNet implementeert.
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + De mogelijkheid om SAS-URL op te halen in het model-object wordt toegevoegd aan de opslag. Ex: model. [get_sas_url ()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Voer uit om uit te voeren. [get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[' gegevens sets '] ophalen van gegevens sets die zijn gekoppeld aan de ingediende uitvoering
    + API toevoegen `Dataset.Tabular` .[ from_json_lines_files ()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) om een TabularDataset te maken op basis van JSON-lijn bestanden. Als u meer informatie wilt over deze tabellaire gegevens in JSON-regel bestanden op TabularDataset, raadpleegt u https://aka.ms/azureml-data voor documentatie.
    + Er zijn extra VM-grootte velden (besturingssysteem schijf, aantal Gpu's) toegevoegd aan de functie [supported_vmsizes ()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Er zijn extra velden toegevoegd aan de functie [list_nodes ()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) om de uitvoering, de privé en het open bare IP-adres, de poort, enzovoort weer te geven.
    + De mogelijkheid om tijdens het maken van het cluster een nieuw veld op te geven tijdens het [inrichten](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)  van een cluster dat kan worden ingesteld op ingeschakeld of uitgeschakeld, afhankelijk van of u de SSH-poort open of gesloten wilt laten. Als u deze niet opgeeft, wordt de poort slim geopend of gesloten, afhankelijk van het feit of u het cluster in een VNet implementeert.
  + **azureml-explain-model**
    + Verbeterde documentatie voor uitleg over uitvoer in het classificatie scenario.
    + De mogelijkheid voor het uploaden van de voorspelde y-waarden is toegevoegd voor de uitleg voor de evaluatie voorbeelden. Hiermee ontgrendelt u nuttige visualisaties.
    + Er is een uitleg eigenschap toegevoegd aan MimicWrapper om het ophalen van de onderliggende MimicExplainer in te scha kelen.
  + **azureml-pipeline-core**
    + Er is een notitie blok toegevoegd om module, ModuleVersion en ModuleStep te beschrijven
  + **azureml-pipeline-steps**
    + RScriptStep toegevoegd ter ondersteuning van het uitvoeren van R-script via AML-pijp lijn.
    + Voor het parseren van vaste meta gegevens parameters in AzureBatchStep die het fout bericht ' toewijzing voor para meter SubscriptionId ' heeft veroorzaakt, is niet opgegeven.
  + **azureml-Train-automl**
    + Ondersteund training_data, validation_data, label_column_name, weight_column_name als invoer indeling voor gegevens
    + Er is een afschaffing bericht toegevoegd voor explain_model () en retrieve_model_explanations ()
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Er is een [notitie blok](https://aka.ms/pl-modulestep) toegevoegd om [module](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29), [ModuleVersion en [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep), te beschrijven.
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + [RScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) toegevoegd ter ondersteuning van het uitvoeren van R-script via AML-pijp lijn.
    + Voor het parseren van vaste meta gegevens parameters in [AzureBatchStep die het fout bericht ' toewijzing voor para meter SubscriptionId ' heeft veroorzaakt, is niet opgegeven.
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + Ondersteunde training_data, validation_data, label_column_name, weight_column_name als invoer indeling voor gegevens.
    + Er is een afschaffing bericht toegevoegd voor [explain_model ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) en [retrieve_model_explanations ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK voor python v-1.0.62

+ **Nieuwe functies**
  + De `timeseries`  Eigenschappen van TabularDataset zijn geïntroduceerd. Met deze eigenschappen kunt u eenvoudig tijds tempel filteren op gegevens een TabularDataset, zoals het nemen van alle gegevens tussen een tijd bereik of de meest recente gegevens.  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb voor een voor beeld van een notebook.
  + Ingeschakelde training met TabularDataset en FileDataset. 

  + **azureml-train-core**
      + Toegevoegd `Nccl` en `Gloo` ondersteund in PyTorch estimator

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + De AutoML-instelling lag_length en de LaggingTransformer is afgeschaft.
    + De juiste validatie van invoer gegevens is hersteld als deze zijn opgegeven in een gegevensstroom indeling
    + De fit_pipeline. py is gewijzigd om de JSON van de grafiek te genereren en te uploaden naar artefacten.
    + De grafiek wordt weer gegeven `userrun` met behulp van `Cytoscape` .
  + **azureml-core**
    + Gestart de verwerking van uitzonde ringen in de ADB-code op en breng wijzigingen aan op basis van de nieuwe fout afhandeling
    + Automatische MSI-verificatie is toegevoegd voor VM-Vm's.
    + Hiermee wordt een bug opgelost waarbij beschadigde of lege modellen kunnen worden geüpload vanwege mislukte pogingen.
    + Er is een fout opgelost waarbij `DataReference` naam verandert wanneer de `DataReference` modus verandert (bijvoorbeeld bij het aanroepen `as_upload` , `as_download` of `as_mount` ).
    + Maken `mount_point` en `target_path` optioneel voor `FileDataset.mount` en `FileDataset.download` .
    + Uitzonde ring dat de time stamp-kolom niet kan worden gevonden, wordt gegenereerd als de time-gerelateerde API wordt aangeroepen zonder dat de toegewezen time stamp-kolommen worden verwijderd.
    + Tijdgebonden kolommen moeten worden toegewezen aan een kolom waarvan het type datum is, anders wordt er een uitzonde ring verwacht
    + Tijdgebonden kolommen voor het toewijzen van de API ' with_timestamp_columns ' kunnen geen waarde krijgen de kolom naam van de verfijnd/ruwe Time Stamp, waardoor eerder toegewezen time stamp-kolommen worden gewist.
    + Er wordt een uitzonde ring gegenereerd wanneer een ruwe korrel of een verfijnde time stamp-kolom wordt verwijderd met de melding dat de gebruiker die het object verwijdert, kan worden uitgevoerd na een time stamp-kolom in de lijst of with_time_stamp met geen waarde voor het vrijgeven van Time Stamp-kolommen
    + Er wordt een uitzonde ring gegenereerd wanneer een ruwe korrel of een verfijnde time stamp-kolom niet is opgenomen in de lijst kolommen behouden, met vermelding van de gebruiker die kan worden uitgevoerd na het opnemen van de time stamp-kolom in de kolom lijst behouden of with_time_stamp met geen waarde voor het vrijgeven van Time Stamp-kolommen.
    + Logboek registratie toegevoegd voor de grootte van een geregistreerd model.
  + **azureml-explain-model**
    + Er is een vaste waarschuwing afgedrukt op de console wanneer het python-pakket ' Pack ' niet is geïnstalleerd: ' ouder dan de ondersteunde versie van lightgbm, een upgrade uitvoeren naar een versie die groter is dan 2.2.1 '
    + Verklaring van een vast Download model met sharding voor globale uitleg met veel functies
    + Er ontbreken voor beelden van de initialisatie van een vaste naimiteerer voor de uitvoer uitleg
    + Vast onveranderbare fout bij het instellen van eigenschappen bij het uploaden met een uitleg-client met behulp van twee verschillende typen modellen
    + Er is een get_raw para meter toegevoegd aan scoreer uitleg. Leg uit dat een beoordelings uitleg zowel de gestuurde als de onbewerkte waarden kan retour neren.
  + **azureml-Train-automl**
    + Geïntroduceerde open bare Api's van AutoML voor het ondersteunen van uitleg van uitleg `automl` SDK: nieuwe manier van ondersteuning van AutoML-uitleg door ontkoppeling van AutoML parametrisatie en uitleg over door SDK geïntegreerde ondersteuning voor onbewerkte gegevens van de geverklaarde SDK voor AutoML-modellen.
    + Het verwijderen van azureml-standaard waarden van een externe trainings omgeving.
    + De standaard locatie van het cache archief is gewijzigd van file cache Store op basis van een AzureFileCacheStore voor AutoML op Azure Databricks codepad.
    + De juiste validatie van invoer gegevens is hersteld als deze zijn opgegeven in een gegevensstroom indeling
  + **azureml-train-core**
    + Geannuleerd source_directory_data_store afschaffing.
    + De mogelijkheid om geïnstalleerde pakket versies van azureml te negeren is toegevoegd.
    + Er is ondersteuning toegevoegd voor dockerfile in de `environment_definition` para meter in de schattingen.
    + Vereenvoudigde gedistribueerde trainings parameters in schattingen.

         ```python
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nieuwe webervaring (preview-versie) voor Azure Machine Learning-werk ruimten
Met de nieuwe webervaring kunnen gegevens wetenschappers en gegevens technici hun end-to-end machine learning levenscyclus volt ooien van bereidt en gegevens visualiseren om modellen op één locatie te trainen en te implementeren.

![Gebruikers interface van Azure Machine Learning werk ruimte (preview)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Belangrijkste functies:**

Met deze nieuwe Azure Machine Learning interface kunt u nu het volgende doen:
+ Uw notitie blokken beheren of een koppeling naar Jupyter
+ [Automatische ML experimenten uitvoeren](tutorial-first-experiment-automated-ml.md)
+ [Gegevens sets maken op basis van lokale bestanden, gegevens opslag, &-Webbe standen](how-to-create-register-datasets.md)
+ Gegevens sets verkennen & voorbereiden voor het maken van modellen
+ Gegevens drift voor uw modellen bewaken
+ Recente resources van een dash board weer geven

Op het moment van deze release worden de volgende browsers ondersteund: Chrome, Firefox, Safari en micro soft Edge Preview.

**Bekende problemen:**

1. Vernieuw uw browser als u ziet dat er iets mis is gegaan. Fout bij het laden van segment bestanden wanneer de implementatie wordt uitgevoerd.

1. Kan het bestand in notitie blokken en bestanden niet verwijderen of een andere naam geven. Tijdens de open bare preview kunt u de Jupyter-gebruikers interface of terminal in notebook VM gebruiken om update Bestands bewerkingen uit te voeren. Omdat het een gekoppeld netwerk bestands systeem is, worden alle wijzigingen doorgevoerd op de VM van het notitie blok in de werk ruimte notebook.

1. SSH in de VM van het notebook:
   1. De SSH-sleutels zoeken die zijn gemaakt tijdens de installatie van de virtuele machine. U kunt ook de sleutels in de Azure Machine Learning werk ruimte vinden > tabblad Compute openen > zoek naar de VM van het notebook in de lijst > de eigenschappen ervan te openen: Kopieer de sleutels uit het dialoog venster.
   1. Importeer deze open bare en persoonlijke SSH-sleutels op uw lokale machine.
   1. Gebruik ze om te SSHen in de VM van het notebook.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK voor python v-1.0.60

+ **Nieuwe functies**
  + Geïntroduceerde FileDataset, die verwijst naar één of meer bestanden in uw gegevens opslag of open bare url's. De bestanden kunnen elk een wille keurige indeling hebben. FileDataset biedt u de mogelijkheid om de bestanden te downloaden of te koppelen aan uw compute. 
  + Ondersteuning voor pijplijn yaml toegevoegd voor PythonScript stap, Adla stap, Databricks stap, DataTransferStep en AzureBatch stap

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + AutoArima is nu een suggestie voor een voor beeld van een pijp lijn.
    + Verbeterde fout rapportage voor prognoses.
    + De logboek registratie is verbeterd door aangepaste uitzonde ringen te gebruiken in plaats van algemeen in de prognose taken.
    + De controle op max_concurrent_iterations is verwijderd zodat deze kleiner is dan het totale aantal herhalingen.
    + AutoML-modellen retour neren nu AutoMLExceptions
    + Met deze release worden de uitvoerings prestaties van automatische machine learning lokale uitvoeringen verbeterd.
  + **azureml-core**
    + Introduceer Dataset.get_all (werk ruimte), die een woorden boek van `TabularDataset` en `FileDataset` objecten retourneert op basis van hun registratie naam.

    ```python
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Als argument inintroducen `parition_format` voor `Dataset.Tabular.from_delimited_files` en `Dataset.Tabular.from_parquet.files` . De partitie gegevens van elk gegevenspad worden geëxtraheerd in kolommen op basis van de opgegeven indeling. {column_name} maakt een teken reeks kolom, en {column_name: JJJJ/MM/DD/uu/mm/SS} maakt de kolom datetime, waarbij ' jjjj ', ' MM ', ' DD ', ' HH ', ' mm ' en ' ss ' worden gebruikt voor het uitpakken van jaar, maand, dag, uur, minuut en seconde voor het type datum/tijd. Het partition_format moet beginnen met de positie van de eerste partitie sleutel tot het einde van het bestandspad. Bijvoorbeeld, op basis van het pad '.. /USA/2019/01/01/data.csv ' waarbij de partitie zich bevindt op land en tijd, partition_format = '/{Country}/{PartitionDate: JJJJ/MM/DD}/data.csv ' maakt teken reeks kolom ' land ' met waarde ' USA ' en datum/tijd kolom ' PartitionDate ' met de waarde ' 2019-01-01 '.
        ```python
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Als argument inintroducen `partition_format` voor `Dataset.Tabular.from_delimited_files` en `Dataset.Tabular.from_parquet.files` . De partitie gegevens van elk gegevenspad worden geëxtraheerd in kolommen op basis van de opgegeven indeling. {column_name} maakt een teken reeks kolom, en {column_name: JJJJ/MM/DD/uu/mm/SS} maakt de kolom datetime, waarbij ' jjjj ', ' MM ', ' DD ', ' HH ', ' mm ' en ' ss ' worden gebruikt voor het uitpakken van jaar, maand, dag, uur, minuut en seconde voor het type datum/tijd. Het partition_format moet beginnen met de positie van de eerste partitie sleutel tot het einde van het bestandspad. Bijvoorbeeld, op basis van het pad '.. /USA/2019/01/01/data.csv ' waarbij de partitie zich bevindt op land en tijd, partition_format = '/{Country}/{PartitionDate: JJJJ/MM/DD}/data.csv ' maakt teken reeks kolom ' land ' met waarde ' USA ' en datum/tijd kolom ' PartitionDate ' met de waarde ' 2019-01-01 '.
    + `to_csv_files` en `to_parquet_files` Er zijn methoden toegevoegd aan `TabularDataset` . Met deze methoden wordt de conversie tussen a `TabularDataset` en a ingeschakeld `FileDataset` door de gegevens te converteren naar bestanden met de opgegeven indeling.
    + Automatisch aanmelden bij het REGI ster van de basis installatie kopie bij het opslaan van een Dockerfile die is gegenereerd door model. pakket ().
    + ' gpu_support ' is niet langer nodig; Met AML wordt nu automatisch de NVIDIA docker-extensie gedetecteerd en gebruikt wanneer deze beschikbaar is. Deze wordt in een toekomstige release verwijderd.
    + Er is ondersteuning toegevoegd om PipelineDrafts te maken, bij te werken en te gebruiken.
    + Met deze release worden de uitvoerings prestaties van automatische machine learning lokale uitvoeringen verbeterd.
    + Gebruikers kunnen metrische gegevens opvragen uit de uitvoerings geschiedenis op naam.
    + De logboek registratie is verbeterd door aangepaste uitzonde ringen te gebruiken in plaats van algemeen in de prognose taken.
  + **azureml-explain-model**
    + Feature_maps para meter toegevoegd aan de nieuwe MimicWrapper, zodat gebruikers de onbewerkte functie verklaringen kunnen ophalen.
    + Gegevensset-uploads zijn nu standaard uitgeschakeld voor het uploaden van uitleg en kunnen opnieuw worden ingeschakeld met upload_datasets = True
    + De para meters voor ' is_law ' zijn toegevoegd aan de uitleg lijst en de functies worden gedownload.
    + Voegt methode `get_raw_explanation(feature_maps)` toe aan zowel globale als lokale uitleg objecten.
    + Versie controle toegevoegd aan lightgbm met afgedrukte waarschuwing als onderstaande ondersteunde versie
    + Geoptimaliseerd geheugen gebruik bij de batch verwerking van uitleg
    + AutoML-modellen retour neren nu AutoMLExceptions
  + **azureml-pipeline-core**
    + Er is ondersteuning toegevoegd om PipelineDrafts te maken, bij te werken en te gebruiken-kan worden gebruikt om onveranderlijke pijplijn definities te onderhouden en ze interactief te gebruiken om uit te voeren
  + **azureml-Train-automl**
    + Er is een functie gemaakt voor het installeren van specifieke versies van GPU-capable pytorch v 1.1.0, :::no-loc text="cuda"::: toolkit 9,0, pytorch-trans formaties, die vereist zijn om Bert/XLNet in te scha kelen in de externe python-runtime omgeving.
  + **azureml-train-core**
    + Vroegtijdige uitval van sommige afstemming definitie fouten rechtstreeks in de SDK in plaats van aan de server zijde.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning data prep SDK v 1.1.14
+ **Oplossingen en verbeteringen voor oplossingen**
  + Schrijven naar ADLS/ADLSGen2 is ingeschakeld met onbewerkte paden en referenties.
  + Er is een fout opgelost die ervoor heeft gezorgd dat deze `include_path=True` niet werkt `read_parquet` .
  + De vaste `to_pandas_dataframe()` fout is veroorzaakt door een uitzonde ring "ongeldige eigenschaps waarde: hostSecret".
  + Er is een fout opgelost waarbij bestanden niet kunnen worden gelezen op DBFS in de modus Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK voor python v-1.0.57
+ **Nieuwe functies**
  + Ingeschakeld `TabularDataset` om te worden gebruikt door AutomatedML. Ga voor meer informatie naar `TabularDataset` https://aka.ms/azureml/howto/createdatasets .

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + U kunt nu het TLS/SSL-certificaat bijwerken voor het Score-eind punt dat is geïmplementeerd op het AKS-cluster, zowel voor het micro soft-certificaat dat is gegenereerd als voor klanten.
  + **azureml-automl-core**
    + Er is een probleem opgelost in AutoML, rijen met ontbrekende labels zijn niet correct verwijderd.
    + Verbeterde logboek registratie van fouten in AutoML; Er worden nu altijd volledige fout berichten naar het logboek bestand geschreven.
    + AutoML heeft het vastmaken van het pakket zodanig bijgewerkt dat het include `azureml-defaults` , `azureml-explain-model` , en `azureml-dataprep` . AutoML ontvangt geen waarschuwing meer over niet-overeenkomende pakket (behalve voor het `azureml-train-automl` pakket).
    + Er is een probleem opgelost `timeseries`  waarbij de AVK gesplitst zijn van een ongelijk groot formaat, waardoor de berekening van de opslag locatie mislukt.
    + Bij het uitvoeren van een ensemble-herhaling voor het multi validatie type training, als we de modellen die zijn getraind op de hele gegevensset niet kunnen downloaden, hebben we een inconsistentie aangetroffen tussen de model gewichten en de modellen die in de stem-ensemble werden ingevoerd.
    + Er is een fout opgelost die optreedt wanneer training en/of validerings labels (y en y_valid) zijn opgenomen in de vorm van Pandas data frame, maar niet als numpy-matrix.
    + Het probleem is opgelost met de prognose taken wanneer er geen is aangetroffen in de Boole-kolommen van de invoer tabellen.
    + Sta AutoML-gebruikers toe om trainings reeksen te verwijderen die niet lang genoeg zijn bij het voors pellen. -Sta AutoML-gebruikers toe om korrels te verwijderen uit de testset die niet voor komt in de Trainingsset tijdens het maken van prognoses.
  + **azureml-core**
    + Probleem opgelost met de volg orde van blob_cache_timeout-para meters.
    + De uitzonderings typen voor externe aanpassing en trans formatie zijn toegevoegd aan systeem fouten.
    + Er is ondersteuning toegevoegd voor Key Vault geheimen voor externe uitvoeringen. Voeg een azureml. core. class kluis. klasse-kluis toe om geheimen toe te voegen, op te halen en weer te geven van de sleutel kluis die is gekoppeld aan uw werk ruimte. Ondersteunde bewerkingen zijn:
      + azureml.core.workspace.Workspace.get_default_keyvault ()
      + azureml.core.keyvault.Keyvault.set_secret (naam, waarde)
      + azureml.core.keyvault.Keyvault.set_secrets (secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret (naam)
      + azureml.core.keyvault.Keyvault.get_secrets (secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets ()
    + Aanvullende methoden voor het verkrijgen van de standaard-sleutel kluis en het ophalen van geheimen tijdens externe uitvoering:
      + azureml.core.workspace.Workspace.get_default_keyvault ()
      + azureml.core.run.Run.get_secret (naam)
      + azureml.core.run.Run.get_secrets (secrets_list)
    + Er zijn extra onderdrukkings parameters toegevoegd voor verzenden-Hyperdrive CLI-opdracht.
    + Verbeter de betrouw baarheid van API-aanroepen om nieuwe pogingen uit te breiden naar algemene aanvragen bibliotheek uitzonderingen.
    + Voeg ondersteuning toe voor het verzenden van uitvoeringen vanuit een ingediende uitvoering.
    + Probleem met een vast verlopen SAS-token in FileWatcher, waardoor bestanden niet meer worden geüpload nadat het oorspronkelijke token is verlopen.
    + Ondersteunde HTTP CSV/TSV-bestanden importeren in dataset python SDK.
    + De methode Workspace. Setup () is afgeschaft. Er wordt een waarschuwings bericht weer gegeven aan gebruikers met de suggesties Create () of Get ()/from_config ().
    + Er zijn Environment.add_private_pip_wheel () toegevoegd, waarmee u persoonlijke aangepaste Python-pakketten kunt uploaden `whl` naar de werk ruimte en deze veilig kunt gebruiken om de omgeving te bouwen/realiseren.
    + U kunt nu het TLS/SSL-certificaat bijwerken voor het Score-eind punt dat is geïmplementeerd op het AKS-cluster, zowel voor het micro soft-certificaat dat is gegenereerd als voor klanten.
  + **azureml-explain-model**
    + Para meter toegevoegd om een model-ID toe te voegen aan uitleg bij het uploaden.
    + Labels zijn toegevoegd `is_raw` aan uitleg in het geheugen en uploads.
    + Er zijn pytorch-ondersteuning en tests toegevoegd voor het pakket voor de azureml-uitleg-model.
  + **azureml-opendatasets**
    + Ondersteuning voor het detecteren en registreren van automatische test omgevingen.
    + U hebt klassen toegevoegd om Amerikaanse populatie per regio en post te krijgen.
  + **azureml-pipeline-core**
    + De eigenschap label is toegevoegd aan de definitie van de invoer-en uitvoer poort.
  + **azureml-telemetry**
    + Er is een onjuiste telemetrie-configuratie hersteld.
  + **azureml-Train-automl**
    + Er is een fout opgetreden bij het uitvoeren van een probleem waarbij tijdens de installatie fouten geen logboek wordt ontvangen van het veld ' Errors ' voor de installatie en daarom niet is opgeslagen in de bovenliggende run ' Errors '.
    + Er is een probleem opgelost in AutoML, rijen met ontbrekende labels zijn niet correct verwijderd.
    + Sta AutoML-gebruikers toe om trainings reeksen te verwijderen die niet lang genoeg zijn bij het voors pellen.
    + Sta AutoML-gebruikers toe om korrels te verwijderen uit de testset die niet voor komt in de Trainingsset tijdens het maken van prognoses.
    + AutoMLStep gaat nu door `automl` naar de back-end om problemen met wijzigingen of toevoegingen van nieuwe configuratie parameters te voor komen.
    + AutoML data Guardrail is nu beschikbaar als open bare preview. De gebruiker ziet een Data Guardrail-rapport (voor classificatie/regressie taken) na de training en is ook toegankelijk via SDK API.
  + **azureml-train-core**
    + Torch 1,2-ondersteuning is toegevoegd in PyTorch Estimator.
  + **azureml-widgets**
    + Verbeterde matrix diagrammen voor Verwar ring voor classificatie trainingen.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning data prep SDK v 1.1.12
+ **Nieuwe functies**
  + Een lijst met teken reeksen kan nu worden door gegeven als invoer voor- `read_*` methoden.

+ **Oplossingen en verbeteringen voor oplossingen**
  + De prestaties van zijn `read_parquet` verbeterd wanneer ze in Spark worden uitgevoerd.
  + Er is een probleem opgelost waarbij `column_type_builder` een fout is opgetreden in een enkele kolom met dubbel zinnige datum notaties.

### <a name="azure-portal"></a>Azure Portal
+ **Preview-functie**
  + Logboek-en uitvoer bestands streaming is nu beschikbaar voor pagina's met details van de uitvoering. De bestanden streamen updates in realtime wanneer de schakel optie voor preview is ingeschakeld.
  + De mogelijkheid om een quotum op een werkruimte niveau in te stellen, wordt vrijgegeven in de preview-versie. AmlCompute quota worden toegewezen op abonnements niveau, maar we bieden u de mogelijkheid om dat quotum tussen werk ruimten te verdelen en toe te wijzen voor eerlijk delen en governance. Klik op de Blade gebruiks namen en **quota's** in de linkernavigatiebalk van uw werk ruimte en selecteer het tabblad **quota's configureren** . U moet een abonnements beheerder zijn om quota's in te kunnen stellen op het niveau van de werk ruimte, omdat dit een bewerking in meerdere werk ruimten is.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK voor python v-1.0.55

+ **Nieuwe functies**
  + Verificatie op basis van tokens wordt nu ondersteund voor de aanroepen van het Score-eind punt dat is geïmplementeerd op AKS. De huidige op sleutels gebaseerde authenticatie blijven wel worden ondersteund en gebruikers kunnen een van deze verificatie mechanismen tegelijk gebruiken.
  + De mogelijkheid om een Blob-opslag te registreren die zich achter het virtuele netwerk (VNet) bevindt als een gegevens opslag.

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + Hiermee wordt een bug opgelost waarbij de validatie grootte voor het verg Roten van de AVK klein is en resulteert in beschadigde, gedicteerde en ware grafieken voor regressie en prognose.
    + De logboek registratie van taken op de externe uitvoering is verbeterd. de gebruiker wordt nu uitgebreid met het fout bericht weer gegeven als de uitvoering is mislukt.
    + Fouten met de `Timeseries` waarde voor preprocess zijn opgelost.
    + Er zijn bepaalde fout berichten voor prognose gegevens validatie meer actie mogelijk.
    + Gereduceerd geheugen gebruik van AutoML wordt uitgevoerd door gegevens sets te verwijderen en/of langzaam te laden, met name tussen het proces
  + **azureml-contrib-uitleg-model**
    + Er is model_task vlag toegevoegd aan uitlegers, zodat de gebruiker de standaard logica voor automatische interferentie voor het model type kan overschrijven
    + Wijzigingen in de widget: automatisch geïnstalleerd met `contrib` , geen ondersteuning meer voor `nbextension` installeren/inschakelen. het is een uitleg van de globale functie (bijvoorbeeld Permutative)
    + Wijzigingen in het dash board:-box plots en viools worden naast het `beeswarm` uitzetten op een overzichts pagina-veel snellere weer gave van de `beeswarm` tekening op de schuif regelaar ' top-k ' wijzigen-nuttig bericht waarin wordt uitgelegd hoe top-k is berekend: bruikbare, aanpas bare berichten in plaats van grafieken wanneer er geen gegevens worden geboden
  + **azureml-core**
    + De methode model. package () is toegevoegd om docker-installatie kopieën en Dockerfiles te maken waarmee modellen en hun afhankelijkheden worden ingekapseld.
    + Lokale webservices bijgewerkt om InferenceConfigs te accepteren die omgevings objecten bevatten.
    + Vast model. REGI ster () waarbij ongeldige modellen worden geproduceerd wanneer. (voor de huidige map) wordt door gegeven als de para meter model_path.
    + Run.submit_child toevoegen, de functionaliteit spiegelt experiment. Submit bij het opgeven van de run as the parent van de ingediende onderliggende run.
    + Ondersteuning voor configuratie opties van model. REGI ster in Run.register_model.
    + De mogelijkheid om JAR-taken uit te voeren op een bestaand cluster.
    + Ondersteunt nu instance_pool_id-en cluster_log_dbfs_path-para meters.
    + Er is ondersteuning toegevoegd voor het gebruik van een omgevings object bij het implementeren van een model naar een webservice. Het omgevings object kan nu worden opgenomen als onderdeel van het InferenceConfig-object.
    + Appinsifht toewijzing toevoegen voor nieuwe regio's-centraal-westus-northcentralus
    + Documentatie toegevoegd voor alle kenmerken in alle Data Store-klassen.
    + Blob_cache_timeout para meter toegevoegd aan `Datastore.register_azure_blob_container` .
    + Save_to_directory-en load_from_directory-methoden toegevoegd aan azureml. core. Environment. Environment.
    + De opdrachten "AZ ml Environment down load" en "AZ ml Environment REGI ster" zijn toegevoegd aan de CLI.
    + Environment.add_private_pip_wheel methode is toegevoegd.
  + **azureml-explain-model**
    + Het bijhouden van gegevensset is toegevoegd aan uitleg met behulp van de DataSet-service (preview).
    + De standaard Batch grootte is verminderd bij het streamen van globale uitleg van 10k tot 100.
    + Er is model_task markering toegevoegd aan uitlegers, zodat de gebruiker de standaard logica voor automatische interferentie voor het model type kan onderdrukken.
  + **azureml-mlflow**
    + Er is een fout opgelost in mlflow.azureml.build_image waarbij geneste mappen worden genegeerd.
  + **azureml-pipeline-steps**
    + De mogelijkheid om JAR-taken uit te voeren op bestaande Azure Databricks cluster is toegevoegd.
    + Er zijn ondersteunings instance_pool_id en cluster_log_dbfs_path-para meters voor de stap DatabricksStep toegevoegd.
    + Er is ondersteuning toegevoegd voor pijplijn parameters in de stap DatabricksStep.
  + **azureml-Train-automl**
    + Toegevoegd `docstrings` voor de aan ensemble gerelateerde bestanden.
    + Documenten bijgewerkt naar een meer geschikte taal voor `max_cores_per_iteration` en `max_concurrent_iterations`
    + De logboek registratie van taken op de externe uitvoering is verbeterd. de gebruiker wordt nu uitgebreid met het fout bericht weer gegeven als de uitvoering is mislukt.
    + Get_data verwijderd uit pijp lijn- `automlstep` notebook.
    + Ondersteuning gestart `dataprep` in `automlstep` .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning data prep SDK v 1.1.10

+ **Nieuwe functies**
  + U kunt nu een aanvraag indienen om specifieke controles uit te voeren (bijvoorbeeld histogram, spreidings plot enz.) op specifieke kolommen.
  + Het argument parallelliseren is toegevoegd aan `append_columns` . Indien waar, worden gegevens in het geheugen geladen, maar wordt de uitvoering parallel uitgevoerd. Als deze eigenschap onwaar is, wordt de uitvoering streaming, maar met één thread.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK voor python v-1.0.53

+ **Nieuwe functies**
  + Automatische Machine Learning ondersteunt nu ONNX modellen voor training op het externe Compute-doel
  + Azure Machine Learning biedt nu de mogelijkheid om de training te hervatten vanuit een vorige uitvoering, controle punt of model bestand.
    + Meer informatie over het [gebruik van schattingen om de training van een vorige uitvoering te hervatten](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + CLI-opdrachten ' model implementeren ' en ' service-update ' accepteren nu para meters, configuratie bestanden of een combi natie van beide. Para meters hebben voor rang op kenmerken in bestanden.
    + De model beschrijving kan nu worden bijgewerkt na de registratie
  + **azureml-automl-core**
    + NimbusML-afhankelijkheid bijwerken naar 1.2.0-versie (meest recent).
    + Ondersteuning voor NimbusML-schattingen toevoegen & pijp lijnen die moeten worden gebruikt binnen AutoML-schattingen.
    + Het oplossen van een bug in de selectie procedure van de ensemble die de resulterende ensemble niet noodzakelijkerwijs groeit, zelfs als de scores nog steeds constant zijn.
    + Het opnieuw gebruiken van een aantal featurizations voor de voor spellingen van taken in CV. Dit versnelt de uitvoerings tijd van de installatie die wordt uitgevoerd door een factor n_cross_validations voor dure featurizations zoals lags en Rolling Windows.
    + Er wordt een probleem opgelost als de tijd buiten de Panda-ondersteunde tijds bereik wordt ondersteund. We veroorzaken nu een DataException als de tijd minder is dan pd. Time Stamp. min of groter dan pd. Time Stamp. Max
    + Door prognoses te maken, kunnen verschillende frequenties worden ingesteld in de trein-en test sets als deze kunnen worden uitgelijnd. Zo kan ' kwar taal vanaf januari ' en op ' kwar taal vanaf oktober ' worden uitgelijnd.
    + De eigenschap para meters is toegevoegd aan de TimeSeriesTransformer.
    + Oude uitzonderings klassen verwijderen.
    + In prognose taken `target_lags` accepteert de para meter nu één geheel getal of een lijst met gehele getallen. Als het gehele getal is gegeven, wordt er slechts één vertraging gemaakt. Als er een lijst wordt weer gegeven, worden de unieke waarden van lags genomen. target_lags = [1, 2, 2, 4] maakt lags van een, twee en vier Peri Oden.
    + Los de fout op over het verliezen van kolom typen na de trans formatie (bug gekoppeld);
    + In `model.forecast(X, y_query)` , toestaan y_query een object type te zijn dat geen (en) bevat aan het begin (#459519).
    + Verwachte waarden toevoegen aan `automl` uitvoer
  + **azureml-contrib-datadrift**
    +  Verbeteringen in het voor beeld van een notebook, inclusief de overstap naar azureml-open datasets in plaats van contrib-open datasets en prestatie verbeteringen bij het verrijken van gegevens
  + **azureml-contrib-uitleg-model**
    + Argument voor vaste trans formaties voor de licht gewicht uitleg van de onbewerkte functie in het contrib-uitleg-model pakket
    + Er zijn segmenten toegevoegd aan de afbeeldings uitleg in de afbeeldings uitleg voor het pakket AzureML-contrib-uitleg-model
    + Scipy sparse-ondersteuning voor LimeExplainer toevoegen
    + Toegevoegd `batch_size` om uitleg te geven wanneer `include_local=False` , voor het streamen van globale toelichtingen in batches om de uitvoerings tijd van DecisionTreeExplainableModel te verbeteren
  + **azureml-contrib-featureengineering**
    + Oplossing voor het aanroepen van set_featurizer_timeseries_params (): wijziging van het type dict-waarde en null-controle: notitie blok toevoegen voor `timeseries`  featurizer
    + NimbusML-afhankelijkheid bijwerken naar 1.2.0-versie (meest recent).
  + **azureml-core**
    + De mogelijkheid om DBFS-gegevens opslag in de AzureML-CLI te koppelen, is toegevoegd
    + De fout opgelost met de Data Store-upload waar een lege map wordt gemaakt als wordt `target_path` gestart met `/`
    + `deepcopy`Probleem opgelost in ServicePrincipalAuthentication.
    + De opdrachten "AZ ml Environment show" en "AZ ml Environment List" zijn toegevoegd aan de CLI.
    + Omgevingen bieden nu ondersteuning voor het opgeven van een base_dockerfile als alternatief voor een al gebouwd base_image.
    + De niet-gebruikte RunConfiguration-instelling auto_prepare_environment is gemarkeerd als afgeschaft.
    + De model beschrijving kan nu worden bijgewerkt na de registratie
    + Bugfix: voor het verwijderen van modellen en installatie kopieën wordt nu meer informatie geboden over het ophalen van upstream-objecten die afhankelijk zijn van het verwijderen als de verwijdering mislukt vanwege een upstream-afhankelijkheid.
    + Er is een probleem opgelost waarbij een lege duur is afgedrukt voor implementaties die optreden bij het maken van een werk ruimte voor sommige omgevingen.
    + Verbeterde fout uitzonderingen voor het maken van werk ruimten. Zo kunnen gebruikers niet zien dat de werk ruimte niet kan worden gemaakt. Kan... niet vinden Als het bericht en wordt in plaats daarvan de daad werkelijke aanmaak fout weer gegeven.
    + Voeg ondersteuning toe voor token verificatie in AKS-webservices.
    + `get_token()`Methode toevoegen aan `Webservice` objecten.
    + CLI-ondersteuning is toegevoegd om machine learning gegevens sets te beheren.
    + `Datastore.register_azure_blob_container` u kunt nu optioneel een `blob_cache_timeout` waarde (in seconden) opgeven waarmee de koppel parameters van de blobfuse worden geconfigureerd om de verval datum van de cache voor dit gegevens archief in te scha kelen. De standaard waarde is geen time-out, zoals wanneer een BLOB wordt gelezen. deze blijft in de lokale cache totdat de taak is voltooid. De meeste taken geven de voor keur aan deze instelling, maar sommige taken moeten meer gegevens lezen uit een grote gegevensset dan op hun knoop punten passen. Voor deze taken helpt deze bij het afstemmen van deze para meter. Wees voorzichtig bij het afstemmen van deze para meter: als u de waarde te laag instelt, kan dit leiden tot slechte prestaties, omdat de gegevens die in een epoche worden gebruikt, mogelijk verlopen voordat ze opnieuw worden gebruikt. Alle Lees bewerkingen worden uitgevoerd vanuit de Blob-opslag/het netwerk in plaats van de lokale cache, die een negatieve invloed heeft op de opleidings tijden.
    + De model beschrijving kan nu na de registratie op de juiste wijze worden bijgewerkt
    + Het model en de installatie kopie bieden nu meer informatie over de upstream-objecten die hiervan afhankelijk zijn, waardoor de verwijdering mislukt
    + Het resource gebruik van externe uitvoeringen verbeteren met behulp van azureml. mlflow.
  + **azureml-explain-model**
    + Argument voor vaste trans formaties voor de licht gewicht uitleg van de onbewerkte functie in het contrib-uitleg-model pakket
    + scipy sparse-ondersteuning voor LimeExplainer toevoegen
    + vorm van lineaire uitleg weergave is toegevoegd, evenals een ander niveau voor de uitleg over lineaire modellen in tabel vorm
    + voor een gesimuleerde uitleg van de uitleg model bibliotheek is een vaste fout opgetreden wanneer include_local = False voor sparse gegevens invoer
    + verwachte waarden toevoegen aan `automl` uitvoer
    + het is belang rijk dat het probleem is opgelost door het argument van de transformaties van de functie RAW te verkrijgen
    + toegevoegd `batch_size` om uitleg te geven wanneer `include_local=False` , voor het streamen van globale toelichtingen in batches om de uitvoerings tijd van DecisionTreeExplainableModel te verbeteren
    + voor de model verklarende bibliotheek is een vast blackbox-uitleg waarbij Pandas data frame invoer vereist is voor de voor spelling
    + Er is een fout opgelost waarbij `explanation.expected_values` soms een zwevende waarde wordt geretourneerd in plaats van een lijst met een zwevende functie.
  + **azureml-mlflow**
    + De prestaties van mlflow.set_experiment verbeteren (experiment_name)
    + Bug corrigeren in het gebruik van InteractiveLoginAuthentication voor mlflow-tracking_uri
    + Het resource gebruik van externe uitvoeringen verbeteren met behulp van azureml. mlflow.
    + De documentatie van het pakket voor de azureml-mlflow verbeteren
    + Patch-bug waarbij mlflow.log_artifacts (' my_dir ') artefacten opslaat onder ' my_dir/<artefact-paden> ' in plaats van ' <artefact-paden> '
  + **azureml-opendatasets**
    + Vastmaken `pyarrow` `opendatasets` aan oude versies (<0.14.0) vanwege geheugen problemen die hier nieuw zijn geïntroduceerd.
    + Verplaats azureml-contrib-opendatasets naar azureml-opendatasets.
    + Toestaan dat open gegevensset-klassen kunnen worden geregistreerd voor Azure Machine Learning werk ruimte en gebruikmaken van de functies van AML-gegevensset naadloos.
    + Verbeter de NoaaIsdWeather-verrijkings prestaties in een niet-SPARK-versie aanzienlijk.
  + **azureml-pipeline-steps**
    + DBFS Data Store wordt nu ondersteund voor invoer en uitvoer in DatabricksStep.
    + Bijgewerkte documentatie voor Azure Batch stap met betrekking tot invoer/uitvoer.
    + In AzureBatchStep is *delete_batch_job_after_finish* standaard waarde gewijzigd in *True*.
  + **azureml-telemetry**
    +  Verplaats azureml-contrib-opendatasets naar azureml-opendatasets.
    + Toestaan dat open gegevensset-klassen kunnen worden geregistreerd voor Azure Machine Learning werk ruimte en gebruikmaken van de functies van AML-gegevensset naadloos.
    + Verbeter de NoaaIsdWeather-verrijkings prestaties in een niet-SPARK-versie aanzienlijk.
  + **azureml-Train-automl**
    + Bijgewerkte documentatie over get_output om het werkelijke retour type weer te geven en aanvullende notities te geven over het ophalen van de sleutel eigenschappen.
    + NimbusML-afhankelijkheid bijwerken naar 1.2.0-versie (meest recent).
    + verwachte waarden toevoegen aan `automl` uitvoer
  + **azureml-train-core**
    + Teken reeksen worden nu geaccepteerd als Compute target voor automatische afstemming-afstemming
    + De niet-gebruikte RunConfiguration-instelling auto_prepare_environment is gemarkeerd als afgeschaft.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning data prep SDK v 1.1.9

+ **Nieuwe functies**
  + Er is ondersteuning toegevoegd voor het lezen van een bestand rechtstreeks vanuit een HTTP-of HTTPS-URL.

+ **Oplossingen en verbeteringen voor oplossingen**
  + Er is een verbeterd fout bericht bij het lezen van een Parquet-gegevensset van een externe bron (die momenteel niet wordt ondersteund).
  + Er is een fout opgelost bij het schrijven naar de Parquet-bestands indeling in ADLS gen 2, en het bijwerken van de naam van de ADLS gen 2-container in het pad.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Visuele interface
+ **Preview-functies**
  + De module voor het uitvoeren van een R-script is toegevoegd in de visuele interface.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK voor python v-1.0.48

+ **Nieuwe functies**
  + **azureml-opendatasets**
    + **azureml-contrib-opengegevenssets** is nu beschikbaar als **azureml-opendatasets**. Het oude pakket kan nog steeds werken, maar we raden u aan om gebruik te maken van **azureml-opengegevenssets** voor geavanceerde mogelijkheden en verbeteringen.
    + Met dit nieuwe pakket kunt u open gegevens sets als gegevensset registreren in Azure Machine Learning werk ruimte en gebruikmaken van de functies die door de gegevensset worden aangeboden.
    + Het omvat ook bestaande mogelijkheden, zoals het gebruik van open gegevens sets als Panda-en SPARK-dataframes en de locatie koppeling voor een bepaalde gegevensset, zoals weer.

+ **Preview-functies**
    + HyperDriveConfig kan pijplijn object nu accepteren als een para meter ter ondersteuning van het afstemmen van afstemming met behulp van een pijp lijn.

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-Train-automl**
    + De fout voor het verliezen van kolom typen na de trans formatie is opgelost.
    + De bug is opgelost zodat y_query een object type is dat aan het begin geen (en) bevat.
    + Het probleem is opgelost in de selectie procedure van ensemble die de resulterende ensemble niet noodzakelijkerwijs groeit, zelfs als de scores nog steeds constant zijn.
    + Het probleem is opgelost met list_models toestaan en list_models instellingen blok keren in AutoMLStep.
    + Het probleem is opgelost waardoor het gebruik van voor verwerking niet mogelijk is wanneer AutoML werd gebruikt in de context van Azure ML-pijp lijnen.
  + **azureml-opendatasets**
    + Azureml-contrib-opendatasets is verplaatst naar azureml-opendatasets.
    + Toegestane open gegevensset-klassen moeten worden geregistreerd voor Azure Machine Learning werk ruimte en kunnen naadloos gebruikmaken van de functies van AML-gegevensset.
    + Verbeterde NoaaIsdWeather-verrijkings prestaties in een niet-SPARK-versie aanzienlijk.
  + **azureml-explain-model**
    + Bijgewerkte online documentatie voor Objects voor interpretaties.
    + Toegevoegd `batch_size` om uitleg te geven wanneer `include_local=False` , voor het streamen van globale toelichtingen in batches, voor het verbeteren van de uitvoerings tijd van DecisionTreeExplainableModel voor de bibliotheek voor de uitleg van modellen.
    + Het probleem opgelost waarbij `explanation.expected_values` soms een float retourneert in plaats van een lijst met een zwevend item.
    + De verwachte waarden zijn toegevoegd aan de `automl` uitvoer voor de gesimuleerde uitleg van de uitleg model bibliotheek.
    + Het kenmerk van de vaste permutatie functie wanneer trans formaties argument worden geleverd om het belang van de onbewerkte functie te verkrijgen.
  + **azureml-core**
    + De mogelijkheid om DBFS-gegevens opslag in de AzureML-CLI te koppelen, is toegevoegd.
    + Het probleem met de upload Data Store is opgelost, waarbij een lege map wordt gemaakt als u `target_path` begint met `/` .
    + Vergelijking van twee gegevens sets is ingeschakeld.
    + Voor het verwijderen van modellen en installatie kopieën wordt nu meer informatie geboden over het ophalen van upstream-objecten die afhankelijk zijn van de methode voor het verwijderen als gevolg van een upstream-afhankelijkheid.
    + De niet-gebruikte RunConfiguration-instelling is afgeschaft in auto_prepare_environment.
  + **azureml-mlflow**
    + Verbeterd resource gebruik van externe uitvoeringen die gebruikmaken van azureml. mlflow.
    + De documentatie van het pakket voor de azureml-mlflow is verbeterd.
    + Er is een probleem opgelost waarbij mlflow.log_artifacts (' my_dir ') artefacten opslaat onder ' my_dir/artifact-paths ' in plaats van ' artefact paden '.
  + **azureml-pipeline-core**
    + De para meter hash_paths voor alle pijplijn stappen is afgeschaft en wordt in de toekomst verwijderd. Standaard wordt de inhoud van de source_directory gehasht (behalve de bestanden in `.amlignore` of `.gitignore` )
    + Herverbetering van module en ModuleStep ter ondersteuning van Compute-specifieke modules, voor bereiding op RunConfiguration-integratie en andere wijzigingen voor het ontgrendelen van het gebruik van reken type-specifiek module in pijp lijnen.
  + **azureml-pipeline-steps**
    + AzureBatchStep: verbeterde documentatie met betrekking tot invoer/uitvoer.
    + AzureBatchStep: delete_batch_job_after_finish standaard waarde is gewijzigd in waar.
  + **azureml-train-core**
    + Teken reeksen worden nu geaccepteerd als Compute target voor automatische afstemming-afstemming.
    + De niet-gebruikte RunConfiguration-instelling is afgeschaft in auto_prepare_environment.
    + Afgeschafte para meters `conda_dependencies_file_path` en `pip_requirements_file_path` ter voor keur van `conda_dependencies_file` en `pip_requirements_file` respectievelijk.
  + **azureml-opendatasets**
    + Verbeter de NoaaIsdWeather-verrijkings prestaties in een niet-SPARK-versie aanzienlijk.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK voor python v 1.0.33 uitgebracht.

+ Azure ML Modellen met hardwareversnelling op [fpga's](how-to-deploy-fpga-web-service.md) is algemeen beschikbaar.
  + U kunt nu [het pakket voor azureml-accel.-modellen gebruiken voor het](how-to-deploy-fpga-web-service.md) volgende:
    + Train de gewichten van een ondersteund diepe Neural-netwerk (ResNet 50, ResNet 152, DenseNet-121, VGG-16 en SSD-VGG)
    + Overboeking Learning gebruiken met de ondersteunde DNN
    + Het model registreren bij Modelbeheer-service en het model container plaatsen
    + Het model implementeren in een Azure-VM met een FPGA in een Azure Kubernetes service-cluster (AKS)
  + De container implementeren op een [Azure data Box Edge](../databox-online/azure-stack-edge-overview.md) server-apparaat
  + Uw gegevens beoordelen met het gRPC-eind punt met dit voor [beeld](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Geautomatiseerde machine learning

+ Functie opruimen om dynamisch toevoegen :::no-loc text="featurizers"::: voor prestatie optimalisatie mogelijk te maken. Nieuw :::no-loc text="featurizers"::: : werk insluitingen, gewicht van bewijs materiaal, doel codering, tekst doel codering, cluster afstand
+ Slimme CV voor het afhandelen van Train/geldige splitsingen binnen automatische ML
+ Weinig geheugen optimalisatie wijzigingen en prestaties verbeteren
+ Prestatie verbetering in model uitleg
+ ONNX model conversie voor lokale uitvoering
+ Ondersteuning voor subsampling toegevoegd
+ Intelligent stoppen wanneer er geen afsluit criteria zijn gedefinieerd
+ Gestapelde ensembles

+ Tijdreeksvoorspelling
  + Nieuwe voor spelling van voor spel functie
  + U kunt nu een cross-Origin-Kruis validatie op tijdreeks gegevens gebruiken
  + Nieuwe functionaliteit toegevoegd om tijd Series lags te configureren
  + Nieuwe functionaliteit toegevoegd ter ondersteuning van statistische functies van het Rolling venster
  + Nieuwe kerst detectie en featurizer wanneer land code is gedefinieerd in de instellingen van het experiment

+ Azure Databricks
  + Ingeschakelde time series-prognose en model explainabilty/interpretatie mogelijkheden
  + U kunt nu door gaan met automatische ML-experimenten en deze hervatten.
  + Er is ondersteuning toegevoegd voor de verwerking van multicore

### <a name="mlops"></a>MLOps
+ **Lokale implementatie & fout opsporing voor Score containers**<br/> U kunt nu een ML-model lokaal implementeren en snel herhalen op uw score bestand en afhankelijkheden om ervoor te zorgen dat ze werken zoals verwacht.

+ **InferenceConfig & model geïntroduceerd. Deploy ()**<br/> Model implementatie biedt nu ondersteuning voor het opgeven van een bronmap met een invoer script, hetzelfde als een RunConfig.  Daarnaast is model implementatie vereenvoudigd tot één opdracht.

+ **Git-referentie tracering**<br/> Klanten hebben een aantal eenvoudige Git-integratie mogelijkheden aangevraagd, omdat het een volledige controle spoor kan bijhouden. We hebben het volgen van alle belangrijkste entiteiten in azure ML geïmplementeerd voor op git gerelateerde meta gegevens (opslag plaats, commit, clean state). Deze informatie wordt automatisch verzameld door de SDK en CLI.

+ **Model profilering &-validatie service**<br/> Klanten klagen vaak het probleem om de reken kracht die is gekoppeld aan de service voor het afwijzen van de afleiding te verg Roten. Met onze model Profiler-service kan de klant voorbeeld invoer leveren en kunnen we een profiel maken van verschillende CPU-of geheugen configuraties om de optimale grootte van de implementatie te bepalen.

+ **Uw eigen basis installatie kopie maken voor afnemen**<br/> Een andere veelvoorkomende klacht is de moeite bij het overstappen van het afzetten van afhankelijkheden. Met onze nieuwe functie voor het delen van basis afbeeldingen kunt u de basis installatie kopieën, afhankelijkheden en alle onderdelen van de proef installatie nu opnieuw gebruiken. Dit versnelt de implementatie en vermindert de ruimte van de binnenste naar de buitenste lus.

+ **Verbeterde ervaring voor het genereren van Swagger-schema's**<br/> Onze vorige Swagger-generatie methode is fout gevoelig en kan niet worden geautomatiseerd. We hebben een nieuwe in line manier om Swagger-schema's te genereren op basis van een python-functie via versieer. We hebben deze code geopend en het schema voor het genereren van de schema's is niet gekoppeld aan het Azure ML-platform.

+ **Azure ML CLI is algemeen beschikbaar (GA)**<br/> Modellen kunnen nu worden geïmplementeerd met één CLI-opdracht. We hebben veelvoorkomende feedback van klanten, waardoor er geen ML-model van een Jupyter-notebook wordt geïmplementeerd. De [**cli-referentie documentatie**](./reference-azure-machine-learning-cli.md) is bijgewerkt.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK voor python v 1.0.30 uitgebracht.

De [`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint) is geïntroduceerd om een nieuwe versie van een gepubliceerde pijp lijn toe te voegen terwijl hetzelfde eind punt wordt gehandhaafd.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + U kunt nu een bestaand script opnieuw verzenden dat wordt uitgevoerd op een bestaand extern Compute-Cluster.
  + U kunt nu een gepubliceerde pijp lijn uitvoeren met nieuwe para meters op het tabblad pijp lijnen.
  + Details van de uitvoering bieden nu ondersteuning voor een nieuwe viewer voor momentopname bestanden. U kunt een moment opname van de Directory weer geven wanneer u een specifieke uitvoering hebt ingediend. U kunt ook het notitie blok downloaden dat is verzonden om de uitvoering te starten.
  + U kunt de bovenliggende uitvoeringen nu annuleren van de Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK voor python v-1.0.23

+ **Nieuwe functies**
  + De SDK van Azure Machine Learning ondersteunt nu python 3,7.
  + Azure Machine Learning DNN-schattingen bieden nu ingebouwde ondersteuning voor meerdere versies. Zo `TensorFlow` accepteert Estimator nu een `framework_version` para meter en kunnen gebruikers versie ' 1,10 ' of ' 1,12 ' opgeven. Voor een lijst met de versies die worden ondersteund door uw huidige SDK-versie, roept u `get_supported_versions()` de gewenste Framework-klasse op (bijvoorbeeld `TensorFlow.get_supported_versions()` ).
  Zie de [DNN Estimator-documentatie](/python/api/azureml-train-core/azureml.train.dnn)voor een lijst met de versies die worden ondersteund door de nieuwste SDK-versie.

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK voor python v-1.0.21

+ **Nieuwe functies**
  + Met de methode *azureml.core.run.create_children* kan meerdere onderliggende uitvoeringen met een lage latentie worden gemaakt met één aanroep.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK voor python v-1.0.18

 + **Wijzigingen**
   + Het azureml-tensorboard-pakket vervangt azureml-contrib-tensorboard.
   + Met deze release kunt u een gebruikers account instellen op uw beheerde Compute-Cluster (amlcompute), terwijl u het maakt. U kunt dit doen door deze eigenschappen door te geven in de inrichtings configuratie. Meer informatie kunt u vinden in de [SDK-referentie documentatie](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning data prep SDK v 1.0.17

+ **Nieuwe functies**
  + Biedt nu ondersteuning voor het toevoegen van twee numerieke kolommen om een resulterende kolom te genereren met behulp van de expressie taal.

+ **Oplossingen en verbeteringen voor oplossingen**
  + De documentatie en parameter controle voor random_split zijn verbeterd.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning data prep SDK v 1.0.16

+ **Bug oplossen**
  + Er is een Service-Principal-verificatie probleem opgelost dat is veroorzaakt door een wijziging in de API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK voor python v-1.0.17

+ **Nieuwe functies**
  + Azure Machine Learning biedt nu ondersteuning voor de eerste klasse voor populaire DNN Framework-ketens. Met behulp van [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer) klasse-gebruikers kunnen ketting modellen eenvoudig worden getraind en geïmplementeerd.
    + Meer informatie over het [uitvoeren van gedistribueerde training met ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)
    + Meer informatie over het [uitvoeren van afstemming tuning with Chainer met behulp van HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning pijp lijnen hebben de mogelijkheid om een pijplijn uitvoering te activeren op basis van wijzigingen in de gegevens opslag. Het [notitie blok](https://aka.ms/pl-schedule) voor de pijp lijn planning is bijgewerkt om deze functie te presen teren.

+ **Oplossingen en verbeteringen voor oplossingen**
  + We hebben ondersteuning toegevoegd in Azure Machine Learning-pijp lijnen voor het instellen van de eigenschap source_directory_data_store op een gewenste gegevens opslag (zoals een Blob-opslag) op [RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration) die worden geleverd aan de [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep). In de volgende stappen wordt gebruikgemaakt van Azure File Store als back-upgegevens opslag, wat kan leiden tot beperking van problemen wanneer een groot aantal stappen gelijktijdig wordt uitgevoerd.

### <a name="azure-portal"></a>Azure Portal

+ **Nieuwe functies**
  + Nieuwe functie voor slepen en neerzetten van tabel editor voor rapporten. Gebruikers kunnen een kolom van het ene naar het tabel gebied slepen waarin een voor beeld van de tabel wordt weer gegeven. De kolommen kunnen opnieuw worden gerangschikt.
  + Viewer voor nieuwe logboek bestanden
  + Koppelingen naar experiment-uitvoeringen, compute, modellen, afbeeldingen en implementaties op het tabblad activiteiten

## <a name="next-steps"></a>Volgende stappen

Lees het overzicht voor [Azure Machine Learning](overview-what-is-azure-ml.md).

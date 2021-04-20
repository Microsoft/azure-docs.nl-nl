---
title: Wat is er nieuw in de release?
titleSuffix: Azure Machine Learning
description: Meer informatie over de nieuwste updates voor Azure Machine Learning en de python machine learning-SDK's voor het voorbereiden van gegevens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: BlackMist
ms.date: 02/18/2021
ms.openlocfilehash: 1de495253dacac5aeab7dcff95f74aeed11782a8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750730"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning opmerkingen bij de release

In dit artikel leert u meer over Azure Machine Learning releases.  Ga voor de volledige SDK-referentie-inhoud Azure Machine Learning de belangrijkste [**SDK**](/python/api/overview/azure/ml/intro) voor Python-referentiepagina van de site.

__RSS-feed__: ontvang een melding wanneer deze pagina wordt bijgewerkt door de volgende URL in uw feedlezer te kopiëren en plakken: `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes%22&locale=en-us`


## <a name="2021-04-19"></a>2021-04-19

### <a name="azure-machine-learning-sdk-for-python-v1270"></a>Azure Machine Learning SDK voor Python v1.27.0
+ **Opgeloste fouten en verbeteringen**
  + **azureml-core**
    + De mogelijkheid toegevoegd om de standaard time-outwaarde voor het uploaden van artefacten te overschrijven via de omgevingsvariabele 'AZUREML_ARTIFACTS_DEFAULT_TIMEOUT'.
    + Er is een fout opgelost waarbij docker-instellingen in het environment-object op ScriptRunConfig niet worden gerespecteerd.
    + Partitionering van een gegevensset toestaan wanneer u deze naar een bestemming kopieert.
    + Er is een aangepaste modus toegevoegd aan outputDatasetConfig om het doorgeven van gemaakte gegevenssets in pijplijnen via een koppelingsfunctie mogelijk te maken. Deze ondersteuningsverbeteringen zijn aangebracht om Tabular Partitioning voor PRS mogelijk te maken.
    + Er is een nieuw KubernetesCompute-rekentype toegevoegd aan azureml-core.
  + **azureml-pipeline-core**
    + Een aangepaste modus toevoegen aan outputDatasetConfig en een gebruiker in staat stellen om gemaakte gegevenssets in pijplijnen door te geven via een koppelingsfunctie. Bestandspadbestemmingen ondersteunen tijdelijke aanduidingen. Deze bieden ondersteuning voor de verbeteringen die zijn aangebracht om tabellaire partitionering voor PRS mogelijk te maken.
    + Toevoeging van nieuw KubernetesCompute-rekentype aan azureml-core.
  + **azureml-pipeline-steps**
    + Toevoeging van nieuw KubernetesCompute-rekentype aan azureml-core.
  + **azureml-synapse**
    + URL van spark-gebruikersinterface bijwerken in widget van azureml synapse
  + **azureml-train-automl-client**
    + De STL-featurizer voor de prognosetaak maakt nu gebruik van een robuustere seizoensgebondenheidsdetectie op basis van de frequentie van de tijdreeks.
  + **azureml-train-core**
    + Er is een fout opgelost waarbij docker-instellingen in het object Environment niet in acht worden genomen.
    + Toevoeging van nieuw KubernetesCompute-rekentype aan azureml-core.


## <a name="2021-04-05"></a>2021-04-05

### <a name="azure-machine-learning-sdk-for-python-v1260"></a>Azure Machine Learning SDK voor Python v1.26.0
+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + Er is een probleem opgelost waarbij Naive-modellen worden aanbevolen in AutoMLStep-uitvoeringen en mislukken met functies voor vertraging of rolling vensters. Deze modellen worden niet aanbevolen wanneer de doelvertraging of de grootte van de doel-rolling-vensters zijn ingesteld.
    +  Console-uitvoer gewijzigd bij het verzenden van een AutoML-run om een portalkoppeling naar de run weer te geven.
  + **azureml-core**
    + HDFS-modus toegevoegd in documentatie.
    + Ondersteuning toegevoegd om inzicht te krijgen in bestandssetpartities op basis van glob-structuur.
    + Er is ondersteuning toegevoegd voor het bijwerken van het containerregister dat is gekoppeld aan de AzureML-werkruimte.
    + Afgeschafte omgevingskenmerken onder dockerSection : 'enabled', 'shared_volume' en 'arguments' maken nu deel uit van DockerConfiguration in RunConfiguration.
    + Documentatie over het klonen van pijplijn-CLI bijgewerkt
    + Portal-URI's bijgewerkt met tenant voor verificatie
    + De naam van het experiment is verwijderd uit run-URI's om omleidingen te voorkomen 
    + Experiment-URO bijgewerkt om experiment-id te gebruiken.
    + Oplossingen voor problemen bij het koppelen van externe berekeningen met AzureML CLI.
    + Portal-URI's bijgewerkt om tenant voor verificatie op te nemen.
    + Experiment-URI bijgewerkt om experiment-id te gebruiken.
  + **azureml-interpret**
    + azureml-interpret bijgewerkt om interpret-community 0.17.0 te gebruiken
  + **azureml-opendatasets**
    + Validatie van begindatum en einddatum en foutindicatie als dit geen datum/tijd-type is.
  + **azureml-parallel-run**
    + [Experimentele functie] Voeg `partition_keys` de parameter toe aan ParallelRunConfig. Indien opgegeven, worden de invoergegevensset(s) gepartitief in minibatch door de sleutels die door de gegevensset zijn opgegeven. Hiervoor moeten alle invoersets worden gepartitiefd.
  + **azureml-pipeline-steps**
    + Bugfix: ondersteuning voor path_on_compute het doorgeven van de configuratie van de gegevensset als download.
    + RScriptStep wordt afgeschaft in plaats van CommandStep te gebruiken voor het uitvoeren van R-scripts in pijplijnen. 
    + EstimatorStep afschaven en CommandStep gebruiken voor het uitvoeren van ML-training (inclusief gedistribueerde training) in pijplijnen.
  + **azureml-sdk**
    + Update python_requires naar < 3.9 voor azureml-sdk
  + **azureml-train-automl-client**
    +  Console-uitvoer gewijzigd bij het verzenden van een AutoML-run om een portalkoppeling naar de run weer te geven.
  + **azureml-train-core**
    + De kenmerken 'enabled', 'shared_volume' en 'arguments' van DockerSection zijn afgeschaft in plaats van DockerConfiguration met ScriptRunConfig te gebruiken.
    +  Gebruik Azure Open Datasets voor MNIST-gegevensset
    + Hyperdrive-foutberichten zijn bijgewerkt.


## <a name="2021-03-22"></a>2021-03-22

### <a name="azure-machine-learning-sdk-for-python-v1250"></a>Azure Machine Learning SDK voor Python v1.25.0
+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    +  Console-uitvoer gewijzigd bij het verzenden van een AutoML-run om een portalkoppeling naar de run weer te geven.
  + **azureml-core**
    + Begint met het bijwerken van het containerregister voor werkruimten in SDK en CLI
    + De kenmerken 'enabled', 'shared_volume' en 'arguments' van DockerSection zijn afgeschaft in plaats van DockerConfiguration met ScriptRunConfig te gebruiken.
    + Documentatie over het klonen van pipeline-CLI bijgewerkt
    + Portal-URI's bijgewerkt met tenant voor verificatie
    + De naam van het experiment is verwijderd uit run-URI's om omleidingen te voorkomen
    + Experiment-URO bijgewerkt om experiment-id te gebruiken.
    + Opgeloste fouten voor het koppelen van externe rekenkracht met az cli
    + Portal-URI's bijgewerkt met tenant voor verificatie.
    + Ondersteuning toegevoegd om inzicht te krijgen in partities voor bestandssets op basis van glob-structuur.
  + **azureml-interpret**
    + azureml-interpret bijgewerkt voor gebruik van interpret-community 0.17.0
  + **azureml-opendatasets**
    + Type validatie en foutindicatie voor begindatum en einddatum van invoer als dit geen datum/tijd-type is.
  + **azureml-pipeline-core**
    + Bugfix: ondersteuning voor path_on_compute tijdens het doorgeven van de configuratie van de gegevensset als download.
  + **azureml-pipeline-steps**
    + Bugfix: ondersteuning voor path_on_compute tijdens het doorgeven van de configuratie van de gegevensset als download.
    + RScriptStep wordt afgeschaft in plaats van CommandStep te gebruiken voor het uitvoeren van R-scripts in pijplijnen. 
    + EstimatorStep afschaven en CommandStep gebruiken voor het uitvoeren van ML-training (inclusief gedistribueerde training) in pijplijnen.
  + **azureml-train-automl-runtime**
    +  Console-uitvoer gewijzigd bij het verzenden van een AutoML-run om een portalkoppeling naar de run weer te geven.
  + **azureml-train-core**
    + De kenmerken 'enabled', 'shared_volume' en 'arguments' van DockerSection zijn afgeschaft in plaats van DockerConfiguration met ScriptRunConfig.
    + Gebruik Azure Open Datasets voor MNIST-gegevensset
    + Hyperdrive-foutberichten zijn bijgewerkt.


## <a name="2021-03-31"></a>2021-03-31
### <a name="azure-machine-learning-studio-notebooks-experience-march-update"></a>Azure Machine Learning Studio Notebooks Experience (update van maart)
+ **Nieuwe functies**
  + CSV/TSV renderen. Gebruikers kunnen een TSV-/CSV-bestand in een rasterindeling renderen voor eenvoudigere gegevensanalyse. 
  + SSO-verificatie voor rekenproces. Gebruikers kunnen nu eenvoudig nieuwe reken-exemplaren rechtstreeks in de Notebook-gebruikersinterface verifiëren, waardoor het eenvoudiger is om Azure SDK's rechtstreeks in AzureML te verifiëren en te gebruiken. 
  + Metrische gegevens reken exemplaar. Gebruikers kunnen metrische gegevens over berekeningen, zoals CPU-gebruik en geheugen, bekijken via de terminal.
  + Bestandsdetails. Gebruikers kunnen nu bestandsdetails zien, waaronder het tijdstip van de laatste wijziging en de bestandsgrootte door op de drie puntjes naast een bestand te klikken.

+ **Opgeloste fouten en verbeteringen**
  + Verbeterde laadtijden voor pagina's.
  + Verbeterde prestaties.
  + Verbeterde snelheid en betrouwbaarheid van kernel.
  + Verticale ruimte verkrijgen door het deelvenster Notebook-bestanden permanent omhoog te verplaatsen
  + Koppelingen kunnen nu worden geklikt in Terminal
  + Verbeterde Prestaties van IntelliSense


## <a name="2021-03-08"></a>2021-03-08

### <a name="azure-machine-learning-sdk-for-python-v1240"></a>Azure Machine Learning SDK voor Python v1.24.0
+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + Achterwaarts compatibele imports verwijderd uit `azureml.automl.core.shared` . Fouten die niet in de module zijn gevonden in de naamruimte kunnen `azureml.automl.core.shared` worden opgelost door te importeren vanuit `azureml.automl.runtime.shared` .
  + **azureml-contrib-automl-dnn-vision**
    + Het yolo-model voor objectdetectie is zichtbaar.
  + **azureml-contrib-dataset**
    + Functionaliteit toegevoegd om gegevenssets in tabelvorm te filteren op kolomwaarden en Bestandsgegevenssets op metagegevens.
  + **azureml-contrib-fairness**
    + JSON-schema opnemen in wheel voor `azureml-contrib-fairness`
  + **azureml-contrib-platform**
    + Als u show_output op Waar bij het implementeren van modellen, worden deferentieconfiguratie en implementatieconfiguratie opnieuw afgespeeld voordat de aanvraag naar de server wordt verzonden.
  + **azureml-core**
    + Functionaliteit toegevoegd om gegevenssets in tabelvorm te filteren op kolomwaarden en Bestandsgegevenssets op metagegevens.
    + Voorheen was het mogelijk dat gebruikers inrichtingsconfiguraties maakten voor ComputeTarget's die niet voldeden aan de vereisten voor wachtwoordsterkte voor het veld (dat wil zeggen dat ze ten minste 3 van de volgende tekens moeten bevatten: 1 kleine letter, 1 hoofdletter, 1 cijfer en 1 speciaal teken uit de `admin_user_password` volgende set: ``\`~!@#$%^&*()=+_[]{}|;:./'",<>?`` ). Als de gebruiker een configuratie met een zwak wachtwoord heeft gemaakt en een taak met die configuratie heeft uitgevoerd, mislukt de taak tijdens runtime. Met de aanroep van wordt nu een met een bijbehorend foutbericht weergegeven waarin `AmlCompute.provisioning_configuration` `ComputeTargetException` de vereisten voor wachtwoordsterkte worden uitgelegd. 
    + Daarnaast was het in sommige gevallen ook mogelijk om een configuratie op te geven met een negatief aantal knooppunten. Het is niet meer mogelijk om dit te doen. Genereert `AmlCompute.provisioning_configuration` nu een als het argument een negatief geheel getal `ComputeTargetException` `max_nodes` is.
    + Als u show_output op Waar bij het implementeren van modellen, worden de deferentieconfiguratie en implementatieconfiguratie weergegeven.
    + Als u show_output op Waar als u wacht op de voltooiing van de modelimplementatie, wordt de voortgang van de implementatiebewerking weergegeven.
    + Door de klant opgegeven Configuratiemap voor AzureML-auth toestaan via omgevingsvariabele: AZUREML_AUTH_CONFIG_DIR
    + Voorheen was het mogelijk om een inrichtingsconfiguratie te maken met het minimumaantal knooppunt dat kleiner is dan het maximumaantal knooppunt. De taak wordt uitgevoerd, maar mislukt tijdens runtime. Deze fout is nu opgelost. Als u nu probeert een inrichtingsconfiguratie te maken met `min_nodes < max_nodes` de SDK, wordt een . `ComputeTargetException`
  + **azureml-interpret**
    + probleem opgelost met uitlegdashboard waarin de statistische functie-belangrijkheid niet wordt weergegeven voor verspreide, samengestelde uitleg
    + geoptimaliseerd geheugengebruik van ExplanationClient in azureml-interpret-pakket
  + **azureml-train-automl-client**
    +  Er is show_output=False opgelost om het besturingselement terug te geven aan de gebruiker bij het uitvoeren met behulp van spark.

## <a name="2021-02-28"></a>2021-02-28
### <a name="azure-machine-learning-studio-notebooks-experience-february-update"></a>Azure Machine Learning Studio Notebooks Experience (update van februari)
+ **Nieuwe functies**
  + [Native Terminal (GA)](./how-to-access-terminal.md). Gebruikers hebben nu toegang tot een geïntegreerde terminal en Git-bewerking via de geïntegreerde terminal.
  + Notebookfragmenten (preview). Algemene Azure ML-codefragmenten zijn nu binnen handbereik beschikbaar. Navigeer naar het deelvenster codefragmenten, dat toegankelijk is via de werkbalk, of activeer het menu codefragmenten met Ctrl + Spatie.  
  + [Sneltoetsen.](./how-to-run-jupyter-notebooks.md#useful-keyboard-shortcuts) Volledige pariteit met sneltoetsen die beschikbaar zijn in Jupyter. 
  + Geef Celparameters aan. Geeft gebruikers weer welke cellen in een notebook parametercellen zijn en kunnen geparameteriseerde notebooks uitvoeren via [Papernon](https://github.com/nteract/papermill) op de reken-instantie.
  + Terminal- en kernelsessiebeheer: gebruikers kunnen alle kernels en terminalsessies beheren die op hun rekenkracht worden uitgevoerd.
  + Knop voor delen. Gebruikers kunnen nu elk bestand in de Bestandenverkenner van Notebook delen door met de rechtermuisknop op het bestand te klikken en de knop Delen te gebruiken.


+ **Opgeloste fouten en verbeteringen**
  + Verbeterde laadtijden voor pagina's
  + Verbeterde prestaties 
  + Verbeterde snelheid en betrouwbaarheid van kernel
  + Er is een draaiwiel toegevoegd om de voortgang voor alle lopende bewerkingen van [het reken exemplaar weer te geven.](./how-to-run-jupyter-notebooks.md#status-indicators)
  + Klik met de rechtermuisknop in Verkenner. Als u met de rechtermuisknop op een bestand klikt, worden nu bestandsbewerkingen geopend. 


## <a name="2021-02-16"></a>2021-02-16

### <a name="azure-machine-learning-sdk-for-python-v1230"></a>Azure Machine Learning SDK voor Python v1.23.0
+ **Opgeloste fouten en verbeteringen**
  + **azureml-core**
    + [Experimentele functie] Ondersteuning toevoegen om synapse-werkruimte als gekoppelde service te koppelen aan AML
    + [Experimentele functie] Ondersteuning toegevoegd voor het koppelen van een Synapse Spark-pool aan AML als rekenkracht
    + [Experimentele functie] Voeg ondersteuning toe voor toegang tot gegevens op basis van identiteit. Gebruikers kunnen gegevensstores of gegevenssets registreren zonder referenties op te geven. In dat geval wordt het AAD-token of de beheerde identiteit van het rekendoel van gebruikers gebruikt voor verificatie. Klik [hier](./how-to-identity-based-data-access.md) voor meer informatie.
  + **azureml-pipeline-steps**
    + [Experimentele functie] Ondersteuning voor [SynapseSparkStep toevoegen](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.synapsesparkstep)
  + **azureml-synapse**
    + [Experimentele functie] Voeg ondersteuning toe voor Spark Magic om een interactieve sessie uit te voeren in de Synapse Spark-pool.
+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-runtime**
    + In deze update hebben we exponentieel vloeiend maken toegevoegd aan de werkset voor prognoses van de AutoML SDK. Op basis van een tijdreeks wordt het beste model geselecteerd door [AICc (Het informatiecriterium van Akaike gecorrigeerd)](https://otexts.com/fpp3/selecting-predictors.html#selecting-predictors) en geretourneerd.
    + AutoML genereert nu twee logboekbestanden in plaats van één. Logboekoverzichten gaan naar de ene of de andere, afhankelijk van het proces waarin de logboekverklaring is gegenereerd.
    + Verwijder onnodige voorbeeldvoorspelling tijdens het trainen van het model met kruisvalidaties. Hierdoor kan de trainingtijd van het model in sommige gevallen afnemen, met name voor prognosemodellen voor tijdreeksen.
  + **azureml-contrib-fairness**
    + Voeg een JSON-schema toe voor de dashboardDictionary-uploads.
  + **azureml-contrib-interpret**
    + leesmij-azureml-contrib-interpret wordt bijgewerkt om aan te geven dat het pakket in de volgende update wordt verwijderd nadat het pakket sinds oktober is afgeschaft. Gebruik in plaats daarvan het pakket azureml-interpret
  + **azureml-core**
    + Voorheen was het mogelijk om een inrichtingsconfiguratie te maken met het minimumaantal knooppunt dat kleiner is dan het maximumaantal knooppunt. Dit is nu opgelost. Als u nu probeert een inrichtingsconfiguratie te maken met `min_nodes < max_nodes` de SDK, wordt er een aan de hand van een . `ComputeTargetException`
    +  Er is een fout opgelost in wait_for_completion in AmlCompute, waardoor de functie de controlestroom retourneerde voordat de bewerking daadwerkelijk was voltooid
    + Run.fail() is nu afgeschaft, gebruik Run.tag() om uitvoeren als mislukt te markeren of gebruik Run.cancel() om de run als geannuleerd te markeren.
    + Foutbericht 'Omgevingsnaam verwachte str, {} gevonden' weergegeven wanneer de opgegeven omgevingsnaam geen tekenreeks is.
  + **azureml-train-automl-client**
    + Er is een fout opgelost waardoor AutoML-experimenten die werden uitgevoerd Azure Databricks clusters niet konden worden geannuleerd.


## <a name="2021-02-09"></a>2021-02-09

### <a name="azure-machine-learning-sdk-for-python-v1220"></a>Azure Machine Learning SDK voor Python v1.22.0
+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + Er is een fout opgelost waarbij een extra PIP-afhankelijkheid werd toegevoegd aan het conda yml-bestand voor Vision-modellen.
  + **azureml-automl-runtime**
    + Er is een fout opgelost waarbij klassieke prognosemodellen (bijvoorbeeld AutoArima) trainingsgegevens konden ontvangen, waarbij rijen met imputed doelwaarden niet aanwezig waren. Hierdoor werd het gegevenscontract van deze modellen geschonden. * Er zijn verschillende fouten opgelost met vertragings-by-occurrence-gedrag in de tijdreeks-vertragingsoperator. Voorheen markeerde de bewerking lag-by-occurrence niet alle imputed rijen correct en genereerde dus niet altijd de juiste occurrence lag-waarden. Er zijn ook compatibiliteitsproblemen opgelost tussen de vertragingsoperator en de operator voor het rolling-venster met vertragings-by-occurrence-gedrag. Dit leidde er eerder toe dat de operator voor het rolling-venster enkele rijen uit de trainingsgegevens moest verwijderen die anders zouden moeten worden gebruikt.
  + **azureml-core**
    + Ondersteuning toegevoegd voor tokenverificatie per doelgroep.
    + Voeg `process_count` toe [aan PyTorchConfiguration om](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) PyTorch-taken met meerdere processen met meerdere knooppunttaken te ondersteunen.
  + **azureml-pipeline-steps**
    + [CommandStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.commandstep) nu GA en niet langer experimenteel.
    + [ParallelRunConfig:](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig)voeg argument toe allowed_failed_count en allowed_failed_percent foutdrempelwaarde op mini-batchniveau te controleren. Foutdrempelwaarde heeft nu drie varianten:
       + error_threshold: het aantal toegestane mislukte mini-batchitems; 
       + allowed_failed_count: het aantal toegestane mislukte minibatchs; 
       + allowed_failed_percent: het percentage toegestane mislukte minibatchs. 
       
       Een taak wordt gestopt als een van deze overschrijdt. error_threshold is vereist om achterwaartse compatibiliteit te behouden. Stel de waarde in op -1 om deze te negeren.
    + Verwerking van witruimten in AutoMLStep-naam opgelost.
    + ScriptRunConfig wordt nu ondersteund door HyperDriveStep
  + **azureml-train-core**
    + HyperDrive-runs die worden aangeroepen vanuit een ScriptRun, worden nu beschouwd als een onderliggende run.
    + Voeg `process_count` toe [aan PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) om PyTorch-taken met meerdere processen met meerdere knooppunttaken te ondersteunen.
  + **azureml-widgets**
    + Voeg widget ParallelRunStepDetails toe om de status van een ParallelRunStep te visualiseren.
    + Hiermee kunnen hyperdrivegebruikers een extra as zien in de grafiek met parallelle coördinaten met de metrische waarde die overeenkomt met elke set hyperparameters voor elke onderliggende run.


 ## <a name="2021-01-31"></a>2021-01-31
### <a name="azure-machine-learning-studio-notebooks-experience-january-update"></a>Azure Machine Learning Studio Notebooks Experience (update van januari)
+ **Nieuwe functies**
  + Native Markdown Editor in AzureML. Gebruikers kunnen nu standaard Markdown-bestanden in AzureML Studio renderen en bewerken.
  + [Knop Uitvoeren voor scripts (.py, . R en .sh).](./how-to-run-jupyter-notebooks.md#run-a-notebook-or-python-script) Gebruikers kunnen nu eenvoudig Python-, R- en Bash-script uitvoeren in AzureML
  + [Variable Explorer.](./how-to-run-jupyter-notebooks.md#explore-variables-in-the-notebook) Verken de inhoud van variabelen en gegevensframes in een pop-upvenster. Gebruikers kunnen eenvoudig het gegevenstype, de grootte en de inhoud controleren.
  + [Tabel met inhoud](./how-to-run-jupyter-notebooks.md#navigate-with-a-toc). Navigeer naar secties van uw notebook, aangegeven door Markdown-headers.
  + Exporteert uw Notebook als ::/HTML/Py. Eenvoudig te delen notebook-bestanden maken door te exporteren naar LaTex, HTML of .py
  + Intellicode. Ml-resultaten bieden een verbeterde [intelligente ervaring voor automatisch aanvullen.](/visualstudio/intellicode/overview)

+ **Opgeloste fouten en verbeteringen**
  + Verbeterde laadtijden voor pagina's
  + Verbeterde prestaties 
  + Verbeterde snelheid en betrouwbaarheid van kernel
  

 ## <a name="2021-01-25"></a>2021-01-25

### <a name="azure-machine-learning-sdk-for-python-v1210"></a>Azure Machine Learning SDK voor Python v1.21.0
+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + Cli-helptekst opgelost bij gebruik van AmlCompute met door de gebruiker toegewezen identiteit
  + **azureml-contrib-automl-dnn-vision**
    + Knoppen voor implementeren en downloaden worden zichtbaar voor AutoML Vision-uitvoeringen en modellen kunnen worden geïmplementeerd of gedownload, vergelijkbaar met andere AutoML-uitvoeringen. Er zijn twee nieuwe bestanden (scoring_file_v_1_0_0.py en conda_env_v_1_0_0.yml) die een script bevatten om de deferencing uit te voeren en een yml-bestand om de Conda-omgeving opnieuw te maken. De naam van het bestand model.pth is ook gewijzigd in de extensie .pt.
  + **azureml-core**
    + MSI-ondersteuning voor azure-cli-ml
    + Ondersteuning voor door de gebruiker toegewezen beheerde identiteit.
    + Met deze wijziging moeten de klanten een door de gebruiker toegewezen identiteit kunnen verstrekken die kan worden gebruikt om de sleutel op te halen uit de sleutelkluis van de klant voor versleuteling in rust.
    +  probleem row_count = 0 opgelost voor het profiel van zeer grote bestanden - fout opgelost in dubbele conversie voor waarden met scheidingstekens met opvulling van witruimte
    + Experimentele vlag verwijderen voor ga naar uitvoerset
    + Documentatie bijwerken over het ophalen van een specifieke versie van een model
    + Bijwerken van werkruimte voor toegang in gemengde modus toestaan in het geval van een privékoppeling
    + Oplossing voor het verwijderen van aanvullende registratie in het gegevensstore voor de functie voor hervatten
    + CLI-/SDK-ondersteuning toegevoegd voor het bijwerken van de door de primaire gebruiker toegewezen identiteit van de werkruimte
  + **azureml-interpret**
    + azureml-interpret bijgewerkt voor interpret-community 0.16.0
    + geheugenoptimalisaties voor uitleg van client in azureml-interpret
  + **azureml-train-automl-runtime**
    + Streaming ingeschakeld voor ADB-runs
  + **azureml-train-core**
    + Oplossing voor het verwijderen van aanvullende registratie in het gegevensstore voor de functie voor hervatten
  + **azureml-widgets**
    + Klanten moeten geen wijzigingen zien in bestaande visualisatie van gegevens uitvoeren met behulp van de widget en krijgen nu ondersteuning als ze eventueel voorwaardelijke hyperparameters gebruiken.
    + De widget Voor het uitvoeren van de gebruiker bevat nu een gedetailleerde uitleg waarom een run de status In de wachtrij heeft.


 ## <a name="2021-01-11"></a>2021-01-11

### <a name="azure-machine-learning-sdk-for-python-v1200"></a>Azure Machine Learning SDK voor Python v1.20.0
+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + framework_version toegevoegd in OptimizationConfig. Het wordt gebruikt wanneer het model is geregistreerd bij framework MULTI.
  + **azureml-contrib-optimization**
    + framework_version toegevoegd in OptimizationConfig. Het wordt gebruikt wanneer het model is geregistreerd bij framework MULTI.
  + **azureml-pipeline-steps**
    + Maak kennis met CommandStep, waarmee de opdracht moet worden verwerkt. De opdracht kan uitvoerbare bestanden, shell-opdrachten, scripts, enzovoort bevatten.
  + **azureml-core**
    + Het maken van werkruimten biedt nu ondersteuning voor door de gebruiker toegewezen identiteit. De UAI-ondersteuning toevoegen vanuit SDK/CLI
    + Er is een probleem opgelost met service.reload() om wijzigingen op te score.py in de lokale implementatie.
    + `run.get_details()` heeft een extra veld met de naam 'submittedBy' waarin de naam van de auteur voor deze run wordt weergegeven.
    + Documentatie voor de methode Model.register bewerkt om te vermelden hoe het model rechtstreeks moet worden geregistreerd
    + Probleem IOT-Server de verwerking van de verbindingsstatus opgelost.
   

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Azure Machine Learning Studio Notebooks Experience (update van december)
+ **Nieuwe functies**
  + Bestandsnaam van gebruiker zoeken. Gebruikers kunnen nu alle bestanden doorzoeken die in een werkruimte zijn opgeslagen.
  + Ondersteuning voor Markdown side-by-side per notebookcel. In een notebookcel kunnen gebruikers nu de mogelijkheid hebben om gerenderde Markdown- en Markdown-syntaxis naast elkaar weer te geven.
  + Celstatusbalk. De statusbalk geeft aan in welke status een codecel zich heeft, of een cel is uitgevoerd en hoe lang het duurde om de cel uit te voeren. 
   
+ **Opgeloste fouten en verbeteringen**
  + Verbeterde laadtijden voor pagina's
  + Verbeterde prestaties 
  + Verbeterde snelheid en betrouwbaarheid van kernel

  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Azure Machine Learning SDK voor Python v1.19.0
+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + Experimentele ondersteuning voor testgegevens toegevoegd aan AutoMLStep.
    + De eerste kern-implementatie van de opnamefunctie van de testset is toegevoegd.
    + Verwijzingen naar sklearn.externals.joblib zijn verplaatst om rechtstreeks afhankelijk te zijn van joblib.
    + introduceert een nieuw AutoML-taaktype van 'image-instance-segmentation'.
  + **azureml-automl-runtime**
    + De eerste kern-implementatie van de opnamefunctie van de testset is toegevoegd.
    + Wanneer alle tekenreeksen in een tekstkolom exact één teken lang zijn, werkt de TfIdf-featurizer voor word-gram niet omdat de tokenizer de tekenreeksen met minder dan 2 tekens negeert. Met de huidige codewijziging kan AutoML deze use-case afhandelen.
    + introduceert een nieuw AutoML-taaktype van 'image-instance-segmentation'.
  + **azureml-contrib-automl-dnn-nlp**
    + Eerste pr voor nieuw dnn-nlp-pakket
  + **azureml-contrib-automl-dnn-vision**
    + introduceert een nieuw AutoML-taaktype van 'image-instance-segmentation'.
  + **azureml-contrib-automl-pipeline-steps**
    + Dit nieuwe pakket is verantwoordelijk voor het maken van stappen die vereist zijn voor een groot aantal modellen voor het trainen/afleiden van scenario's. - Ook wordt de code train/deference verplaatst naar het pakket azureml.train.automl.runtime, zodat toekomstige oplossingen automatisch beschikbaar zijn via gecureerde omgevingsreleases.
  + **azureml-contrib-dataset**
    + introduceert een nieuw AutoML-taaktype van 'image-instance-segmentation'.
  + **azureml-core**
    + De eerste kern-implementatie van de opnamefunctie van de testset is toegevoegd.
    + De xref-waarschuwingen voor documentatie in het azureml-core-pakket oplossen
    + Doc string fixes for Command support feature in SDK
    + Opdracht-eigenschap toevoegen aan RunConfiguration. Met de functie kunnen gebruikers een werkelijke opdracht of uitvoerbare bestanden uitvoeren op de rekenkracht via AzureML SDK.
    + Gebruikers kunnen een leeg experiment verwijderen op de id van dat experiment.
  + **azureml-dataprep**
    + Er is ondersteuning toegevoegd voor gegevenssets voor Spark die is gebouwd met Scala 2.12. Hiermee wordt de bestaande 2.11-ondersteuning toegevoegd.
  + **azureml-mlflow**
    + AzureML-MLflow veilige beveiliging in externe scripts toegevoegd om vroegtijdige beëindiging van ingediende runs te voorkomen.
  + **azureml-pipeline-core**
    + Er is een fout opgelost bij het instellen van een standaardpijplijn voor een pijplijn-eindpunt dat is gemaakt via de gebruikersinterface
  + **azureml-pipeline-steps**
    + Experimentele ondersteuning voor testgegevens toegevoegd aan AutoMLStep.
  + **azureml-tensorboard**
    + De xref-waarschuwingen voor documentatie in het azureml-core-pakket oplossen
  + **azureml-train-automl-client**
    + Experimentele ondersteuning voor testgegevens toegevoegd aan AutoMLStep.
    + De eerste kern-implementatie van de opnamefunctie van de testset is toegevoegd.
    + introduceert een nieuw AutoML-taaktype van 'image-instance-segmentation'.
  + **azureml-train-automl-runtime**
    + De eerste kern-implementatie van de opnamefunctie van de testset is toegevoegd.
    + Herstel de berekening van de onbewerkte uitleg voor het beste AutoML-model als de AutoML-modellen worden getraind met behulp validation_size instelling.
    + Verwijzingen verplaatst naar sklearn.externals.joblib om rechtstreeks afhankelijk te zijn van joblib.
  + **azureml-train-core**
    + HyperDriveRun.get_children_sorted_by_primary_metric() moet nu sneller worden voltooid
    + Verbeterde foutafhandeling in HyperDrive SDK.
    +  Alle estimatorklassen zijn afgeschaft en hebben scriptRunConfig gebruikt om experimentuit runs te configureren. Afgeschafte klassen zijn onder andere:
        + MMLBase
        + Estimator
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + Het gebruik van Nccl en Gloo als geldige invoertypen voor estimator-klassen is afgeschaft en pyTorchConfiguration met ScriptRunConfig is niet meer gebruikt.
    + Het gebruik van Mpi als een geldig invoertype voor estimator-klassen is afgeschaft tenure van het gebruik van MpiConfiguration met ScriptRunConfig.
    + Opdracht-eigenschap toevoegen aan runconfiguration. Met de functie kunnen gebruikers een werkelijke opdracht of uitvoerbare bestanden uitvoeren op de berekening via de AzureML SDK.

    +  Alle estimatorklassen zijn afgeschaft en hebben scriptRunConfig niet meer gebruikt om experimentuit runs te configureren. Afgeschafte klassen zijn onder andere: + MMLBaseEstimator + Estimator + PyTorch + TensorFlow + Chainer + SKLearn
    + Het gebruik van Nccl en Gloo als een geldig type invoer voor Estimator-klassen is afgeschaft tenure van het gebruik van PyTorchConfiguration met ScriptRunConfig. 
    + Het gebruik van Mpi als een geldig type invoer voor Estimator-klassen is afgeschaft tenure van het gebruik van MpiConfiguration met ScriptRunConfig.

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Azure Machine Learning Studio Notebooks Experience (update van november)
+ **Nieuwe functies**
   + Native Terminal. Gebruikers hebben nu toegang tot een geïntegreerde terminal en Git-bewerking via de [geïntegreerde terminal.](./how-to-access-terminal.md)
  + Dubbele map 
  + De vervolgkeuzekeuze voor Compute 
  + Offline Compute Pylance 

+ **Opgeloste fouten en verbeteringen**
  + Verbeterde laadtijden voor pagina's
  + Verbeterde prestaties 
  + Verbeterde snelheid en betrouwbaarheid van kernel
  + Grote bestand uploaden. U kunt nu een bestand >95 MB uploaden

## <a name="2020-11-09"></a>2020-11-09

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Azure Machine Learning SDK voor Python v1.18.0
+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    +  Verbeterde verwerking van korte tijdreeksen doordat deze kunnen worden opvulling met gaussische ruis.
  + **azureml-automl-runtime**
    + Throw ConfigException als een datum/tijd-kolom outOfBoundsDatetime-waarde heeft
    + Verbeterde verwerking van korte tijdreeksen door ze op te geven met gaussische ruis.
    + Ervoor zorgen dat elke tekstkolom een char-gram-transformatie kan gebruiken met het n-gram-bereik op basis van de lengte van de tekenreeksen in die tekstkolom
    + Uitleg over onbewerkte functies voor de beste modus voor AutoML-experimenten die worden uitgevoerd op de lokale berekening van de gebruiker
  + **azureml-core**
    + Maak het pakket vast: pyjwt om te voorkomen dat er in toekomstige releases versies worden binnenhalen die fouten maken.
    + Als u een experiment maakt, wordt het actieve of laatste gearchiveerde experiment met dezelfde opgegeven naam als een dergelijk experiment bestaat of een nieuw experiment.
    + Als get_experiment naam aanroept, wordt het actieve of laatste gearchiveerde experiment met die naam retourneert.
    + Gebruikers kunnen de naam van een experiment niet wijzigen tijdens het opnieuw activeren.
    + Verbeterd foutbericht met mogelijke oplossingen wanneer een gegevensset onjuist wordt doorgegeven aan een experiment (bijvoorbeeld ScriptRunConfig). 
    + Verbeterde documentatie voor `OutputDatasetConfig.register_on_complete` om het gedrag op te nemen van wat er gebeurt wanneer de naam al bestaat.
    + Het opgeven van de invoer- en uitvoernamen van gegevenssets die kunnen botsen met algemene omgevingsvariabelen, leidt nu tot een waarschuwing
    + Een nieuwe `grant_workspace_access` parameter gebruiken bij het registreren van gegevensstores. Stel deze in op om toegang te krijgen tot gegevens achter het virtuele `True` netwerk vanuit Machine Learning Studio.
      [Meer informatie](./how-to-enable-studio-virtual-network.md)
    + Gekoppelde service-API is verfijnd. In plaats van resource-id op te geven, hebben we drie afzonderlijke parameters sub_id, rg en naam die zijn gedefinieerd in de configuratie.
    + Als u wilt dat klanten problemen met tokenbe beschadiging zelf kunnen oplossen, moet u synchronisatie van het werkruimte-token inschakelen als een openbare methode.
    + Door deze wijziging kan een lege tekenreeks worden gebruikt als een waarde voor een script_param
  + **azureml-train-automl-client**
    +  Verbeterde verwerking van korte tijdreeksen doordat deze kunnen worden opvulling met gaussische ruis.
  + **azureml-train-automl-runtime**
    + Throw ConfigException als een datum/tijd-kolom outOfBoundsDatetime-waarde heeft
    + Ondersteuning toegevoegd voor het bieden van uitleg over onbewerkte functies voor het beste model voor AutoML-experimenten die worden uitgevoerd op de lokale berekening van de gebruiker
    + Verbeterde verwerking van korte tijdreeksen doordat deze kunnen worden opvulling met gaussische ruis.
  + **azureml-train-core**
    + Door deze wijziging kan een lege tekenreeks worden gebruikt als een waarde voor een script_param
  + **azureml-train-restclients-hyperdrive**
    + README is gewijzigd om meer context te bieden
  + **azureml-widgets**
    + Voeg ondersteuning voor tekenreeksen toe aan de bibliotheek grafieken/parallelle coördinaten voor widget.

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>Gegevenslabels voor segmentatie van afbeeldings-exemplaren (veelhoekannotatie) (preview)

Het projecttype segmentatie van het exemplaar van de afbeelding (veelhoekaantekeningen) in gegevenslabels is nu beschikbaar, zodat gebruikers kunnen tekenen en aantekeningen kunnen maken met veelhoeken rond de contour van de objecten in de afbeeldingen. Gebruikers kunnen een klasse en een veelhoek toewijzen aan elk object dat van belang is in een afbeelding.

Meer informatie over [segmenteringslabels voor het exemplaar van een afbeelding.](how-to-label-images.md)



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Azure Machine Learning SDK voor Python v1.17.0
+ **nieuwe voorbeelden**
  + Een nieuwe community-gestuurde opslagplaats met voorbeelden is beschikbaar op https://github.com/Azure/azureml-examples
+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + Er is een probleem opgelost waarbij get_output een XGBoostError kan doen.
  + **azureml-automl-runtime**
    + Functies op basis van tijd/agenda die door AutoML zijn gemaakt, hebben nu het voorvoegsel .
    + Er is een IndexError opgelost die zich voordeed tijdens de training van StackEnsemble voor classificatie-gegevenssets met een groot aantal klassen en subsampling ingeschakeld.
    + Er is een probleem opgelost waarbij VotingRegressor-voorspellingen onnauwkeurig kunnen zijn na refitting van het model.
  + **azureml-core**
    + Aanvullende details toegevoegd over de relatie tussen de configuratie van de AKS-implementatie en Azure Kubernetes Service concepten.
    + Ondersteuning voor omgevingsclientlabels. De gebruiker kan omgevingen labelen en er op label naar verwijzen.
  + **azureml-dataprep**
    + Beter foutbericht bij gebruik van momenteel niet-ondersteunde Spark met Scala 2.12.
  + **azureml-explain-model**
    + Het pakket azureml-explain-model is officieel afgeschaft
  + **azureml-mlflow**
    + Er is een fout in mlflow.projects.run opgelost op de back-end azureml, waarbij de status niet correct is verwerkt.
  + **azureml-pipeline-core**
    + Voeg ondersteuning toe om een pijplijnschema te maken, weer te geven en op te halen op basis van één pijplijn-eindpunt.
    +  Verbeterde documentatie van PipelineData.as_dataset met een ongeldig gebruiksvoorbeeld: als u PipelineData.as_dataset onjuist gebruikt, wordt er nu een ValueException geworpen
    + Het notebook voor HyperDriveStep-pijplijnen is gewijzigd om het beste model in een PipelineStep te registreren, direct na de uitvoering van HyperDriveStep.
  + **azureml-pipeline-steps**
    + Het notebook voor HyperDriveStep-pijplijnen is gewijzigd om het beste model te registreren in een PipelineStep direct na de uitvoering van HyperDriveStep.
  + **azureml-train-automl-client**
    + Er is een probleem opgelost waarbij get_output een XGBoostError zou kunnen doen.

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Azure Machine Learning Studio Notebooks Experience (update van oktober)
+ **Nieuwe functies**
  + [Volledige ondersteuning voor virtuele netwerken](./how-to-enable-studio-virtual-network.md)
  + [Focusmodus](./how-to-run-jupyter-notebooks.md#focus-mode)
  + Notebooks opslaan met Ctrl+S
  + Regelnummers

+ **Opgeloste fouten en verbeteringen**
  + Verbetering van snelheid en betrouwbaarheid van kernel
  + Updates van de gebruikersinterface van de Jupyter-widget

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Azure Machine Learning SDK voor Python v1.16.0
+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + AKSWebservice en AKSEndpoints ondersteunen nu cpu- en geheugenresourcelimieten op podniveau. Deze optionele limieten kunnen worden gebruikt door `--cpu-cores-limit` markeringen en in te stellen `--memory-gb-limit` in toepasselijke CLI-aanroepen
  + **azureml-core**
    + Belangrijke versies van directe afhankelijkheden van azureml-core vastmaken
    + AKSWebservice en AKSEndpoints ondersteunen nu cpu- en geheugenresourcelimieten op podniveau. Meer informatie over [Kubernetes-resources en -limieten](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)
    + Bijgewerkte run.log_table zodat afzonderlijke rijen kunnen worden geregistreerd.
    + Statische methode toegevoegd `Run.get(workspace, run_id)` om een run alleen op te halen met behulp van een werkruimte 
    + Instantiemethode toegevoegd `Workspace.get_run(run_id)` om een run binnen de werkruimte op te halen
    + Introductie van de opdracht-eigenschap in de run-configuratie waarmee gebruikers opdracht in te dienen in plaats van script & argumenten.
  + **azureml-interpret**
    + probleem opgelost met uitleg is_raw gedrag van clientvlag in azureml-interpret
  + **azureml-sdk**
    + `azureml-sdk` officieel ondersteuning voor Python 3.8.
  + **azureml-train-core**
    + TensorFlow 2.3-gecureerde omgeving toevoegen
    + Introductie van de opdracht-eigenschap in de run-configuratie waarmee gebruikers opdracht kunnen verzenden in plaats van script & argumenten.
  + **azureml-widgets**
    + De interface voor het uitvoeren van scripts is opnieuw ontworpen.


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Azure Machine Learning SDK voor Python v1.15.0
+ **Opgeloste fouten en verbeteringen**
  + **azureml-contrib-interpret**
    + DE UITLEG IS VERPLAATST van azureml-contrib-interpret naar interpret-community package and image explainer removed from azureml-contrib-interpret package
    + visualisatiedashboard verwijderd uit pakket azureml-contrib-interpret, uitlegclient verplaatst naar azureml-interpret-pakket en afgeschaft in azureml-contrib-interpret-pakket en notebooks bijgewerkt om verbeterde API weer te geven
    + pypi-pakketbeschrijvingen opgelost voor azureml-interpret, azureml-explain-model, azureml-contrib-interpret en azureml-tensorboard
  + **azureml-contrib-notebook**
    + Maak de afhankelijkheid vast aan < 6 zodat papertrör 1.x blijft werken.
  + **azureml-core**
    + Parameters toegevoegd aan de constructor TensorflowConfiguration en MpiConfiguration om een meer gestroomlijnde initialisatie van de klassekenmerken mogelijk te maken zonder dat de gebruiker elk afzonderlijk kenmerk moet instellen. Er is een PyTorchConfiguration-klasse toegevoegd voor het configureren van gedistribueerde PyTorch-taken in ScriptRunConfig.
    + Maak de versie van azure-mgmt-resource vast om de verificatiefout op te lossen.
    + Ondersteuning voor Triton No Code Deploy
    + uitvoerma directories die zijn opgegeven in Run.start_logging() worden nu bij het gebruik van uitvoeren in interactieve scenario's. De bij te houden bestanden zijn zichtbaar in ML Studio bij het aanroepen van Run.complete()
    + Bestandscoderen kan nu worden opgegeven tijdens het maken van de gegevensset met `Dataset.Tabular.from_delimited_files` en door het argument door te `Dataset.Tabular.from_json_lines_files` `encoding` geven. De ondersteunde coderingen zijn 'utf8', 'iso88591', 'latin1', 'ascii', utf16', 'utf32', 'utf8metrische' en 'windows1252'.
    + Opgeloste fout wanneer het omgevingsobject niet wordt doorgegeven aan de ScriptRunConfig-constructor.
    + Run.cancel() bijgewerkt om het annuleren van een lokale run vanaf een andere computer toe te staan.
  + **azureml-dataprep**
    +  Time-outproblemen met de gegevenssets zijn opgelost.
  + **azureml-explain-model**
    + pypi-pakketbeschrijvingen opgelost voor azureml-interpret, azureml-explain-model, azureml-contrib-interpret en azureml-tensorboard
  + **azureml-interpret**
    + visualisatiedashboard verwijderd uit pakket azureml-contrib-interpret, uitlegclient verplaatst naar azureml-interpret-pakket en afgeschaft in azureml-contrib-interpret-pakket en notebooks bijgewerkt met verbeterde API
    + azureml-interpret-pakket bijgewerkt om afhankelijk te zijn van interpret-community 0.15.0
    + pypi-pakketbeschrijvingen opgelost voor azureml-interpret, azureml-explain-model, azureml-contrib-interpret en azureml-tensorboard
  + **azureml-pipeline-core**
    +  Probleem met pijplijn opgelost waarbij het systeem mogelijk niet meer reageert wanneer wordt aangeroepen met de parameter ingesteld op de naam van een `OutputFileDatasetConfig` `register_on_complete` bestaande `name` gegevensset.
  + **azureml-pipeline-steps**
    + Verouderde Databricks-notebooks verwijderd.
  + **azureml-tensorboard**
    + pypi-pakketbeschrijvingen opgelost voor azureml-interpret, azureml-explain-model, azureml-contrib-interpret en azureml-tensorboard
  + **azureml-train-automl-runtime**
    + visualisatiedashboard verwijderd uit pakket azureml-contrib-interpret, uitlegclient verplaatst naar azureml-interpret-pakket en afgeschaft in azureml-contrib-interpret-pakket en notebooks bijgewerkt met verbeterde API
  + **azureml-widgets**
    + visualisatiedashboard verwijderd uit pakket azureml-contrib-interpret, uitlegclient verplaatst naar azureml-interpret-pakket en afgeschaft in azureml-contrib-interpret-pakket en notebooks bijgewerkt met verbeterde API

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Azure Machine Learning SDK voor Python v1.14.0
+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + Rasterprofilering is verwijderd uit de SDK en wordt niet meer ondersteund.
  + **azureml-accel-models**
    + azureml-accel-models-pakket ondersteunt nu Tensorflow 2.x
  + **azureml-automl-core**
    + Foutafhandeling toegevoegd in get_output gevallen waarin lokale versies van pandas/sklearn niet overeenkomen met de versies die tijdens de training zijn gebruikt
  + **azureml-automl-runtime**
    + Er is een fout opgelost waarbij AutoArima-iteraties zouden mislukken met een PredictionException en het bericht : "Stille fout opgetreden tijdens de voorspelling."
  + **azureml-cli-common**
    + Rasterprofilering is verwijderd uit de SDK en wordt niet meer ondersteund.
  + **azureml-contrib-server**
    + De beschrijving van het pakket voor de pypi-overzichtspagina is bijgewerkt.
  + **azureml-core**
    + Rasterprofilering is verwijderd uit de SDK en wordt niet meer ondersteund.
    + Verminder het aantal foutberichten wanneer het ophalen van de werkruimte mislukt.
    + Er wordt geen waarschuwing weergegeven wanneer het ophalen van metagegevens mislukt
    + Nieuwe Kusto-stap en Kusto-rekendoel.
    + Werk het document bij voor de SKU-parameter. Verwijder de SKU in de updatefunctionaliteit van de werkruimte in CLI en SDK.
    + De beschrijving van het pakket voor de pypi-overzichtspagina is bijgewerkt.
    + Bijgewerkte documentatie voor AzureML-omgevingen.
    + Stel instellingen voor door de service beheerde resources beschikbaar voor de AML-werkruimte in de SDK.
  + **azureml-dataprep**
    + Machtiging voor uitvoeren inschakelen voor bestanden voor het maken van gegevenssets.
  + **azureml-mlflow**
    + Documentatie en notebookvoorbeelden voor AzureML MLflow bijgewerkt 
    + Nieuwe ondersteuning voor MLflow-projecten met AzureML-back-end
    + Ondersteuning voor MLflow-modelregister
    + Azure RBAC-ondersteuning toegevoegd voor AzureML-MLflow bewerkingen 
    
  + **azureml-pipeline-core**
    + Verbeterde documentatie van de PipelineOutputFileDataset.parse_*-methoden.
    + Nieuwe Kusto-stap en Kusto-rekendoel.
    + Opgegeven Swaggerurl-eigenschap voor pijplijn-eindpuntentiteit via die gebruiker kan de schemadefinitie voor het gepubliceerde pijplijn-eindpunt zien.
  + **azureml-pipeline-steps**
    + Nieuwe Kusto-stap en Kusto-rekendoel.
  + **azureml-telemetry**
    + De beschrijving van het pakket voor de pypi-overzichtspagina is bijgewerkt.
  + **azureml-train**
    + De beschrijving van het pakket voor de pypi-overzichtspagina is bijgewerkt.
  + **azureml-train-automl-client**
    + Foutafhandeling toegevoegd in get_output gevallen waarin lokale versies van pandas/sklearn niet overeenkomen met de versies die tijdens de training zijn gebruikt
  + **azureml-train-core**
    + De beschrijving van het pakket voor de pypi-overzichtspagina is bijgewerkt.
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK voor Python v1.13.0
+ **Preview-functies**
  + **azureml-core** Met de nieuwe mogelijkheid voor uitvoersets kunt u terugschrijven naar cloudopslag, waaronder Blob, ADLS Gen 1, ADLS Gen 2 en FileShare. U kunt configureren waar gegevens moeten worden uitgevoerd, hoe gegevens kunnen worden uitgevoerd (via een mount of upload), of u de uitvoergegevens wilt registreren voor toekomstig hergebruik en delen en tussenliggende gegevens naadloos tussen pijplijnstappen wilt doorgeven. Dit maakt reproduceerbaarheid en delen mogelijk, voorkomt duplicatie van gegevens en resulteert in kostenefficiëntie en productiviteitsverbeteringen. [Meer informatie over het gebruik ervan](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)
    
+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + Het validated_{platform}_requirements.txt toegevoegd voor het vastmaken van alle pip-afhankelijkheden voor AutoML.
    + Deze release ondersteunt modellen groter dan 4 Gb.
    + AutoML-afhankelijkheden bijgewerkt: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-automl-runtime**
    + Stel horovod in voor tekst-DNN om altijd fp16-compressie te gebruiken.
    + Deze release ondersteunt modellen groter dan 4 Gb.
    + Probleem opgelost waarbij AutoML mislukt met ImportError: kan naam niet `RollingOriginValidator` importeren.
    + AutoML-afhankelijkheden bijgewerkt: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-contrib-automl-dnn-forecasting**
    + AutoML-afhankelijkheden bijgewerkt: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-contrib-fairness**
    + Geef een korte beschrijving op voor azureml-contrib-fairness.
  + **azureml-contrib-pipeline-steps**
    + Bericht toegevoegd dat aangeeft dat dit pakket is afgeschaft en dat de gebruiker in plaats daarvan azureml-pipeline-steps moet gebruiken.
  + **azureml-core**
    + Lijstsleutelopdracht toegevoegd voor werkruimte.
    + Voeg de parameter tags toe in de Werkruimte-SDK en CLI.
    + De fout opgelost waarbij het verzenden van een onderliggende run met Dataset mislukt vanwege `TypeError: can't pickle _thread.RLock objects` .
    + Standaard page_count documentatie toevoegen voor Model list().
    + Wijzig de CLI&SDK om de parameter adbworkspace en add workspace adb lin/unlink runner te nemen.
    + Er is een fout in Dataset.update opgelost waardoor de nieuwste versie van de gegevensset is bijgewerkt, niet de versie van de gegevenssetupdate is aangeroepen. 
    + Er is een fout in Dataset.get_by_name die de tags voor de nieuwste gegevenssetversie zou tonen, zelfs wanneer een specifieke oudere versie werd opgehaald.
  + **azureml-interpret**
    + Waarschijnlijkheidsuitvoer toegevoegd voor het vormgeven van scoring-uitleg in azureml-interpret op basis van shap_values_output parameter van de oorspronkelijke uitleg.
  + **azureml-pipeline-core**
    + Verbeterde `PipelineOutputAbstractDataset.register` documentatie.
  + **azureml-train-automl-client**
    + AutoML-afhankelijkheden bijgewerkt: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-train-automl-runtime**
    + AutoML-afhankelijkheden bijgewerkt: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-train-core**
    + Gebruikers moeten nu een geldige hyperparameter_sampling arg bij het maken van een HyperDriveConfig. Bovendien is de documentatie voor HyperDriveRunConfig bewerkt om gebruikers te informeren over de afschaffing van HyperDriveRunConfig.
    + Standaardversie van PyTorch terugschakelen naar 1.4.
    + PyTorch 1.6 toevoegen & Tensorflow 2.2-afbeeldingen en gecureerde omgeving.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Azure Machine Learning Studio Notebooks Experience (update van augustus)
+ **Nieuwe functies**
  + Nieuwe landingspagina aan de slag 
  
+ **Preview-functies**
    + Functie verzamelen in Notebooks. Met de [functie Gather](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) kunnen gebruikers nu eenvoudig notebooks ops schonen met. Gather maakt gebruik van een geautomatiseerde afhankelijkheidsanalyse van uw notebook, zodat de essentiële code behouden blijft, maar irrelevante onderdelen worden verwijderd.

+ **Opgeloste fouten en verbeteringen**
  + Verbetering van snelheid en betrouwbaarheid
  + Fouten in donkere modus opgelost
  + Fouten in de uitvoerscrol opgelost
  + Met Voorbeeld zoeken wordt nu door alle inhoud van alle bestanden in de Azure Machine Learning notebooks gezocht
  + R-cellen met meerdere lijnen kunnen nu worden uitgevoerd
  + 'Ik vertrouw de inhoud van dit bestand' is nu automatisch gecontroleerd na de eerste keer
  + Verbeterd dialoogvenster conflictoplossing, met de nieuwe optie 'Een kopie maken'
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK voor Python v1.12.0

+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + Voeg image_name en image_label toe aan Model.package() om de naam van de ingebouwde pakketafbeelding te wijzigen.
  + **azureml-automl-core**
    + AutoML veroorzaakt een nieuwe foutcode van dataprep wanneer inhoud wordt gewijzigd terwijl deze wordt gelezen.
  + **azureml-automl-runtime**
    + Waarschuwingen toegevoegd voor de gebruiker wanneer gegevens ontbrekende waarden bevatten, maar featurization is uitgeschakeld.
    + Er zijn fouten met onderliggende run opgelost wanneer gegevens nan bevatten en featurization is uitgeschakeld.
    + AutoML veroorzaakt een nieuwe foutcode van dataprep wanneer inhoud wordt gewijzigd terwijl deze wordt gelezen.
    + Normalisatie bijgewerkt voor prognoses van metrische gegevens die per grain moeten worden uitgevoerd.
    + Verbeterde berekening van prognose-kwantielen wanneer lookback-functies zijn uitgeschakeld.
    + Probleem opgelost met afhandeling van sparse matrix bij het berekenen van uitleg na AutoML.
  + **azureml-core**
    + Een nieuwe methode `run.get_detailed_status()` toont nu de gedetailleerde uitleg van de huidige uitvoeringsstatus. Er wordt momenteel alleen uitleg over de `Queued` status weergegeven.
    + Voeg image_name en image_label toe aan Model.package() om de naam van de ingebouwde pakketafbeelding te wijzigen.
    + Nieuwe methode `set_pip_requirements()` om de volledige pip-sectie in één [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies) keer in te stellen.
    + Registratie van referentie-minder inschakelen ADLS Gen2 gegevensstore.
    + Verbeterd foutbericht wanneer u probeert een onjuist gegevenssettype te downloaden of te mounten.
    + Voorbeeldnotenotenote voor het filteren van tijdreeksgegevenssets bijwerken met meer voorbeelden van partition_timestamp die filteroptimalisatie biedt.
    + Wijzig de SDK en CLI om subscriptionId, resourceGroup, workspaceName, peConnectionName als parameters te accepteren in plaats van ArmResourceId bij het verwijderen van de verbinding met het privé-eindpunt.
    + Experimental Settingator toont de klassenaam voor eenvoudigere identificatie.
    + Beschrijvingen voor de assets in Modellen worden niet meer automatisch gegenereerd op basis van een uitvoering.
  + **azureml-datadrift**
    + Markeer create_from_model API in DataDriftDetector als afgeschaft.
  + **azureml-dataprep**
    + Verbeterd foutbericht wanneer u probeert een onjuist gegevenssettype te downloaden of te mounten.
  + **azureml-pipeline-core**
    + Er is een fout opgelost bij het deserialiseren van een pijplijngrafiek die geregistreerde gegevenssets bevat.
  + **azureml-pipeline-steps**
    + RScriptStep ondersteunt RSection vanuit azureml.core.environment.
    + De parameter passthru_automl_config verwijderd uit de `AutoMLStep` openbare API en geconverteerd naar een interne alleen-parameter.
  + **azureml-train-automl-client**
    + Lokale asynchrone, beheerde omgevingen zijn verwijderd uit AutoML. Alle lokale runs worden uitgevoerd in de omgeving waarin de run is gestart.
    + Problemen met momentopnamen opgelost bij het verzenden van AutoML-runs zonder door de gebruiker geleverde scripts.
    + Er zijn fouten met onderliggende run opgelost wanneer gegevens nan en featurization zijn uitgeschakeld.
  + **azureml-train-automl-runtime**
    + AutoML veroorzaakt een nieuwe foutcode van dataprep wanneer inhoud wordt gewijzigd terwijl deze wordt gelezen.
    + Problemen met momentopnamen opgelost bij het verzenden van AutoML-runs zonder door de gebruiker geleverde scripts.
    + Er zijn fouten met onderliggende run opgelost wanneer gegevens nan en featurization zijn uitgeschakeld.
  + **azureml-train-core**
    + Ondersteuning toegevoegd voor het opgeven van pip-opties (bijvoorbeeld --extra-index-url) in het pip-vereistenbestand dat is doorgegeven aan een [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator) `pip_requirements_file` through-parameter.


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK voor Python v1.11.0

+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + Probleem opgelost met model framework en model framework dat niet is doorgegeven in run-object in CLI-modelregistratiepad
    + Cli-opdracht amlcompute identity show opgelost om tenant-id en principal-id weer te geven 
  + **azureml-train-automl-client**
    + Er get_best_child toegevoegd aan AutoMLRun voor het ophalen van de beste onderliggende uitvoering voor een AutoML-uitvoering zonder het bijbehorende model te downloaden.
    + Er is een ModelProxy-object toegevoegd waarmee voorspellen of voorspellen kan worden uitgevoerd in een externe trainingsomgeving zonder het model lokaal te downloaden.
    + Onverhandelde uitzonderingen in AutoML wijzen nu op een HTTP-pagina met bekende problemen, waar meer informatie over de fouten kan worden gevonden.
  + **azureml-core**
    + Modelnamen kunnen 255 tekens lang zijn.
    + Environment.get_image_details() retourobjecttype gewijzigd. `DockerImageDetails` klasse `dict` vervangen, afbeeldingsdetails zijn beschikbaar in de nieuwe klasse-eigenschappen. Wijzigingen zijn achterwaarts compatibel.
    + Fout opgelost voor Environment.from_pip_requirements() om de afhankelijkhedenstructuur te behouden
    + Er is een fout opgelost waarbij log_list zou mislukken als een int en double in dezelfde lijst zouden worden opgenomen.
    + Houd er rekening mee dat als er rekendoelen zijn gekoppeld aan de werkruimte, deze doelen niet werken als ze zich niet achter hetzelfde virtuele netwerk als het privé-eindpunt van de werkruimte hebben.
    + Optioneel `as_named_input` gemaakt bij het gebruik van gegevenssets in experimenten en toegevoegd aan en `as_mount` `as_download` `FileDataset` . De invoernaam wordt automatisch gegenereerd als `as_mount` of `as_download` wordt aangeroepen.
  + **azureml-automl-core**
    + Onverhandelde uitzonderingen in AutoML wijzen nu op een HTTP-pagina met bekende problemen, waar meer informatie over de fouten kan worden gevonden.
    + Er get_best_child () toegevoegd aan AutoMLRun voor het ophalen van de beste onderliggende uitvoering voor een AutoML-uitvoering zonder het bijbehorende model te downloaden.
    + Er is een ModelProxy-object toegevoegd waarmee voorspellen of voorspellen kan worden uitgevoerd in een externe trainingsomgeving zonder het model lokaal te downloaden.
  + **azureml-pipeline-steps**
    + De `enable_default_model_output` vlaggen en zijn toegevoegd aan `enable_default_metrics_output` `AutoMLStep` . Deze vlaggen kunnen worden gebruikt om de standaarduitvoer in of uit te schakelen.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK voor Python v1.10.0

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + Als bij het gebruik van AutoML een pad wordt doorgegeven aan het AutoMLConfig-object en het nog niet bestaat, wordt het automatisch gemaakt.
    + Gebruikers kunnen nu een tijdreeksfrequentie opgeven voor prognosetaken met behulp van de `freq` parameter .
  + **azureml-automl-runtime**
    + Wanneer u AutoML gebruikt en een pad wordt doorgegeven aan het AutoMLConfig-object en het nog niet bestaat, wordt het automatisch gemaakt.
    + Gebruikers kunnen nu een tijdreeksfrequentie opgeven voor het voorspellen van taken met behulp van de `freq` parameter .
    + AutoML-prognoses bieden nu ondersteuning voor rolling evaluatie, wat van toepassing is op de use-case dat de lengte van een test of validatieset langer is dan de invoerperiode en bekende y_pred-waarde wordt gebruikt als context voor prognoses.
  + **azureml-core**
    + Waarschuwingsberichten worden afgedrukt als er tijdens een run geen bestanden zijn gedownload uit de gegevensstore.
    + Documentatie voor `skip_validation` toegevoegd aan de `Datastore.register_azure_sql_database method` .
    + Gebruikers moeten upgraden naar sdk v1.10.0 of hoger om een automatisch goedgekeurd privé-eindpunt te maken. Dit omvat de Notebook-resource die achter het VNet kan worden gebruikt.
    + NotebookInfo beschikbaar maken in het antwoord van werkruimte op halen.
    + Wijzigingen om aanroepen te hebben om rekendoelen weer te krijgen en rekendoel te krijgen slaagt bij een externe run. Sdk-functies om rekendoel- en lijstwerkruimte-rekendoelen op te halen, werken nu in externe runs.
    + Afschaffingsberichten toevoegen aan de klassebeschrijvingen voor azureml.core.image-klassen.
    + Uitzonderings- en opschoonwerkruimte en afhankelijke resources als het maken van een privé-eindpunt van de werkruimte mislukt.
    + Ondersteuning voor upgrade van werkruimte-SKU in updatemethode voor werkruimte.
  + **azureml-datadrift**
    + Matplotlib-versie bijgewerkt van 3.0.2 naar 3.2.1 om python 3.8 te ondersteunen.
  + **azureml-dataprep**
    + Ondersteuning toegevoegd voor web-URL-gegevensbronnen met `Range` of `Head` aanvraag. 
    + Verbeterde stabiliteit voor het toevoegen en downloaden van bestandssets.
  + **azureml-train-automl-client**
    + Problemen opgelost met betrekking tot het verwijderen `RequirementParseError` van uit setuptools.
    + Gebruik docker in plaats van conda voor lokale runs die zijn ingediend met 'compute_target='local'"
    + De duur van de iteratie die op de console wordt afgedrukt, is gecorrigeerd. Voorheen werd de duur van de iteratie soms afgedrukt als eindtijd van de run min de aanmaaktijd van de run. Deze is gecorrigeerd om gelijk te zijn aan de eindtijd van de run min de begintijd van de run.
    + Wanneer u AutoML gebruikt en een pad wordt doorgegeven aan het AutoMLConfig-object en het nog niet bestaat, wordt het automatisch gemaakt.
    + Gebruikers kunnen nu een tijdreeksfrequentie opgeven voor prognosetaken met behulp van de `freq` parameter .
  + **azureml-train-automl-runtime**
    + Verbeterde console-uitvoer wanneer de uitleg van het beste model mislukt.
    + De naam van de invoerparameter is gewijzigd in 'blocked_models' om een gevoelige term te verwijderen.
      + De naam van de invoerparameter is gewijzigd in 'allowed_models' om een gevoelige term te verwijderen.
    + Gebruikers kunnen nu een tijdreeksfrequentie opgeven voor prognosetaken met behulp van de `freq` parameter .

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK voor Python v1.9.0

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + Vervangen get_model_path() door AZUREML_MODEL_DIR omgevingsvariabele in autogenerated scoring-script van AutoML. Er is ook telemetrie toegevoegd om fouten tijdens init() bij te houden.
    + De mogelijkheid om op te geven `enable_cache` als onderdeel van AutoMLConfig is verwijderd
    + Er is een fout opgelost waarbij kan mislukken met servicefouten tijdens specifieke prognoses
    + Verbeterde foutafhandeling voor specifieke modellen tijdens `get_output`
    + Aanroep van fitted_model.fit(X, y) voor classificatie met y-transformer opgelost
    + Aangepaste forward fill-functie ingeschakeld voor prognosetaken
    + Er wordt een nieuwe klasse ForecastingParameters gebruikt in plaats van prognoseparameters in een dict-indeling
    + Verbeterde autodesectie doelvertraging
    + Beperkte beschikbaarheid toegevoegd van multi-noded gedistribueerde featurization met meerdere gpu's met
  + **azureml-automl-runtime**
    + Bij Haar worden nu additieve seizoensgebonden modellen gemaakt in plaats van vermenigvuldigend.
    + Het probleem opgelost bij korte grains, omdat frequenties die verschillen van die van de lange grainn leiden tot mislukte runs.
  + **azureml-contrib-automl-dnn-vision**
    + Systeem-/gpu-statistieken en logboekgemiddelden verzamelen voor training en scoren
  + **azureml-contrib-platform**
    + Ondersteuning toegevoegd voor vlag enable-app-insights in ManagedInferencing
  + **azureml-core**
    + Een validatieparameter voor deze API's doordat validatie kan worden overgeslagen wanneer de gegevensbron niet toegankelijk is vanaf de huidige berekening.
      + TabularDataset.time_before(end_time, include_boundary=True, validate=True)
      + TabularDataset.time_after(start_time, include_boundary=True, validate=True)
      + TabularDataset.time_recent(time_delta, include_boundary=True, validate=True)
      + TabularDataset.time_between(start_time, end_time, include_boundary=True, validate=True)
    + Ondersteuning voor frameworkfiltering toegevoegd voor modellijst en NCD AutoML-voorbeeld toegevoegd in notebook
    + Voor Datastore.register_azure_blob_container en Datastore.register_azure_file_share (alleen opties die ondersteuning bieden voor SAS-token), hebben we de doc-tekenreeksen voor het veld bijgewerkt met minimale machtigingsvereisten voor typische lees- en `sas_token` schrijfscenario's.
    + De _with_auth in ws.get_mlflow_tracking_uri()
  + **azureml-mlflow**
    + Ondersteuning voor het implementeren van lokale file://-modellen met AzureML-MLflow
    + De _with_auth in ws.get_mlflow_tracking_uri()
  + **azureml-opendatasets**
    + Onlangs gepubliceerde Covid-19-traceringssets zijn nu beschikbaar met de SDK
  + **azureml-pipeline-core**
    + Waarschuwing voor afmelden wanneer 'azureml-defaults' niet is opgenomen als onderdeel van pip-afhankelijkheid
    + Het weergeven van notitie verbeteren.
    + Ondersteuning toegevoegd voor regeluitbreekingen tussen aangehaalde regels bij het parseren van bestanden met scheidingstekens naar PipelineOutputFileDataset.
    + De klasse PipelineDataset is afgeschaft. Voor meer informatie raadpleegt u https://aka.ms/dataset-deprecation. Zie voor meer informatie over het gebruik van een gegevensset met een https://aka.ms/pipeline-with-dataset pijplijn.
  + **azureml-pipeline-steps**
    + Doc updates voor azureml-pipeline-steps.
    +  Ondersteuning toegevoegd in ParallelRunConfig voor gebruikers om omgevingen inline te definiëren met de rest van de `load_yaml()` configuratie of in een afzonderlijk bestand
  + **azureml-train-automl-client**.
    + De mogelijkheid om op te `enable_cache` geven als onderdeel van AutoMLConfig is verwijderd
  + **azureml-train-automl-runtime**
    + Beperkte beschikbaarheid toegevoegd van multi-noded gedistribueerde featurization met meerdere gpu's met DEFS.
    + Foutafhandeling toegevoegd voor niet-compatibele pakketten in op ADB gebaseerde geautomatiseerde machine learning uitgevoerd.
  + **azureml-widgets**
    + Updates voor azureml-widgets documenteert.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK voor Python v1.8.0
  
  + **Preview-functies**
    + **azureml-contrib-fairness** Het `azureml-contrib-fairness` pakket biedt integratie tussen het opensource-pakket [fairlearn](https://fairlearn.github.io) en Azure Machine Learning-studio. Met het pakket kunnen dashboards voor modelfairness-evaluatie worden geüpload als onderdeel van een AzureML-uitvoering en worden weergegeven in Azure Machine Learning-studio

+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + Ondersteuning voor het verkrijgen van logboeken van de Init-container.
    + Nieuwe CLI-opdrachten toegevoegd voor het beheren van ComputeInstance
  + **azureml-automl-core**
    + Gebruikers kunnen nu stack-ensemble-iteratie inschakelen voor tijdreekstaken met een waarschuwing dat er mogelijk overfitting kan optreden.
    + Er is een nieuw type gebruikers-uitzondering toegevoegd dat zich voordeed als er met de inhoud van de cacheopslag is geknoeid
  + **azureml-automl-runtime**
    + Class Balancing Sweeping wordt niet meer ingeschakeld als de gebruiker featurization uit schakelen.  
  + **azureml-contrib-itp**
    + Het rekentype CmAks wordt ondersteund. U kunt uw eigen AKS-cluster koppelen aan de werkruimte voor de trainingstaken.
  + **azureml-contrib-notebook**
    + Documentverbeteringen in het pakket azureml-contrib-notebook.
  + **azureml-contrib-pipeline-steps**
    + Documentverbeteringen in het pakket azureml-contrib--pipeline-steps.
  + **azureml-core**
    + Functies set_connection, get_connection, list_connections, delete_connection toevoegen voor klanten om te werken met de resource voor de werkruimteverbinding
    + Documentatie-updates voor het pakket azureml-coore/azureml.exceptions.
    + Documentatie-updates voor azureml-core-pakket.
    + Documentupdates voor ComputeInstance-klasse.
    + Documentverbeteringen in het pakket azureml-core/azureml.core.compute.
    + Documentverbeteringen voor webservicegerelateerde klassen in azureml-core.
    + Door de gebruiker geselecteerde gegevensopslag ondersteunen voor het opslaan van profileringsgegevens
    + De eigenschap expand en page_count toegevoegd voor api voor modellijst
    + Er is een fout opgelost waarbij het verwijderen van de eigenschap overschrijven ervoor zorgt dat de verzonden run mislukt met deserialisatiefout.
    + Er is een inconsistente mapstructuur opgelost bij het downloaden of mounten van een FileDataset die naar één bestand verwijst.
    + Het laden van een gegevensset met Parquet-to_spark_dataframe gaat nu sneller en ondersteunt alle parquet- en Spark SQL-gegevenstypen.
    + Ondersteuning voor het verkrijgen van logboeken van de Init-container.
    + AutoML-runs zijn nu gemarkeerd als onderliggende run van parallelle run-stap.
  + **azureml-datadrift**
    + Documentverbeteringen in het pakket azureml-contrib-notebook.
  + **azureml-dataprep**
    + Het laden van een gegevensset met Parquet-to_spark_dataframe gaat nu sneller en ondersteunt alle parquet- en Spark SQL-gegevenstypen.
    + Betere geheugenverwerking voor outOfMemory-probleem voor to_pandas_dataframe.
  + **azureml-interpret**
    + Azureml-interpret bijgewerkt voor het gebruik van interpret-community versie 0.12.*
  + **azureml-mlflow**
    + Documentverbeteringen in azureml-mlflow.
    + Voegt ondersteuning toe voor het AML-modelregister met MLFlow.
  + **azureml-opendatasets**
    + Ondersteuning toegevoegd voor Python 3.8
  + **azureml-pipeline-core**
    + De `PipelineDataset` documentatie van is bijgewerkt om duidelijk te maken dat het een interne klasse is.
    + ParallelRunStep-updates voor het accepteren van meerdere waarden voor één argument, bijvoorbeeld: "--group_column_names", "Col1", "Col2", "Col3"
    + De vereiste passthru_automl_config voor het gebruik van tussenliggende gegevens met AutoMLStep in pijplijnen verwijderd.
  + **azureml-pipeline-steps**
    + Documentverbeteringen in het pakket azureml-pipeline-steps.
    + De vereiste passthru_automl_config voor het gebruik van tussenliggende gegevens met AutoMLStep in pijplijnen verwijderd.
  + **azureml-telemetry**
    + Documentverbeteringen in azureml-telemetry.
  + **azureml-train-automl-client**
    + Er is een fout opgelost `experiment.submit()` waarbij tweemaal aangeroepen op `AutoMLConfig` een object leidde tot ander gedrag.
    + Gebruikers kunnen nu stack-ensemble-iteratie inschakelen voor tijdreekstaken met een waarschuwing dat er mogelijk overfitting kan optreden.
    + Gedrag van AutoML-run is gewijzigd om UserErrorException te verhogen als de service een gebruikersfout veroorzaakt
    + Lost een fout op waardoor azureml_automl.log niet is gegenereerd of logboeken ontbreken bij het uitvoeren van een AutoML-experiment op een extern rekendoel.
    + Voor classificatiegegevenssets met klassen die niet in balans zijn, passen we Gewichtsverdeling toe. Als de functieopserfunctie bepaalt dat voor gegevens met subsampled gewichtsverdeling de prestaties van de classificatietaak met een bepaalde drempelwaarde verbetert.
    + AutoML-runs zijn nu gemarkeerd als onderliggende run van Parallelle run-stap.
  + **azureml-train-automl-runtime**
    + Gedrag van AutoML-run is gewijzigd om UserErrorException te verhogen als de service een gebruikersfout veroorzaakt
    + AutoML-runs zijn nu gemarkeerd als onderliggende run van Parallelle run-stap.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK voor Python v1.7.0

+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + Het verwijderen van modelprofilering is voltooid door CLI-opdrachten en pakketafhankelijkheden op te ruimen. Modelprofilering is beschikbaar in de kern.
    + De minimumversie van Azure CLI wordt geupgraded naar 2.3.0
  + **azureml-automl-core**
    + Beter uitzonderingsbericht over de featurization fit_transform() vanwege aangepaste transformatieparameters.
    + Er is ondersteuning voor meerdere talen voor deep learning-transformermodellen zoals ML in geautomatiseerde ML.
    + Verwijder de afgeschafte parameter lag_length uit de documentatie.
    + De documentatie voor prognoseparameters is verbeterd. De parameter lag_length is afgeschaft.
  + **azureml-automl-runtime**
    + De fout opgelost die is opgetreden wanneer een van de categorische kolommen leeg is in de prognose-/testtijd.
    + Los de mislukte run op wanneer de lookback-functies zijn ingeschakeld en de gegevens korte grains bevatten.
    + Probleem opgelost met een foutbericht over de index met dubbele tijd wanneer vertragingen of doorrollende vensters werden ingesteld op 'auto'.
    + Het probleem met De modellen Voor en Arima in gegevenssets met de lookback-functies is opgelost.
    + Ondersteuning toegevoegd voor datums vóór 1677-09-21 of na 2262-04-11 in kolommen, overige datum/tijd in de prognosetaken. Verbeterde foutberichten.
    + De documentatie voor prognoseparameters is verbeterd. De lag_length parameter is afgeschaft.
    + Beter uitzonderingsbericht over featurization fit_transform() vanwege aangepaste transformatorparameters.
    + Er is ondersteuning voor meerdere talen voor deep learning-transformermodellen zoals ML in geautomatiseerde ML.
    + Cachebewerkingen die leiden tot een aantal OSErrors, leiden tot een gebruikersfout.
    + Controles toegevoegd om ervoor te zorgen dat trainings- en validatiegegevens hetzelfde aantal en dezelfde set kolommen hebben
    + Probleem opgelost met het automatisch gegenereerde AutoML-scorescript wanneer de gegevens aanhalingstekens bevatten
    + Het inschakelen van uitleg voor AutoML- En ensemblemodellen die Het Model van Het Model bevatten.
    + Een recent klantprobleem heeft een live-site-fout aan het licht laten komen, waarbij we berichten samen met Class-Balancing-Sweeping melden, zelfs wanneer de klasseverdelingslogica niet juist is ingeschakeld. Deze logboeken/berichten verwijderen met deze pr.
  + **azureml-cli-common**
    + Het verwijderen van modelprofilering uit de cli-opdrachten en pakketafhankelijkheden is voltooid. Modelprofilering is beschikbaar in de kern.
  + **azureml-contrib-reinforcementlearning**
    + Hulpprogramma voor belastingstests
  + **azureml-core**
    + Documentatiewijzigingen in Script_run_config.py
    + Lost een fout op met het afdrukken van de uitvoer van de CLI voor het uitvoeren van submit-pipeline
    + Documentatieverbeteringen voor azureml-core/azureml.data
    + Probleem opgelost bij het ophalen van een opslagaccount met behulp van de opdracht hdfs getconf
    + Verbeterde register_azure_blob_container en register_azure_file_share documentatie
  + **azureml-datadrift**
    + Verbeterde implementatie voor het uitschakelen en inschakelen van monitors voor gegevenssetdrift
  + **azureml-interpret**
    + In uitleg van client verwijdert u NaN's of Infs vóór json-serialisatie bij het uploaden van artefacten
    + Werk bij naar de nieuwste versie van interpret-community om fouten met het geheugen te verbeteren voor globale uitleg met veel functies en klassen
    + Voeg true_ys optionele parameter toe aan de uitleg bij het uploaden om aanvullende functies in te stellen in de studio-gebruikersinterface
    + Prestaties download_model_explanations() en list_model_explanations() verbeteren
    + Kleine aanpassingen aan notebooks, om te helpen bij het debuggen
  + **azureml-opendatasets**
    + azureml-opendatasets heeft azureml-dataprep versie 1.4.0 of hoger nodig. Waarschuwing toegevoegd als er een lagere versie wordt gedetecteerd
  + **azureml-pipeline-core**
    + Door deze wijziging kan de gebruiker een optionele runconfig aan de moduleVersion verstrekken bij het aanroepen van de module. Publish_python_script.
    + Een knooppuntaccount inschakelen kan een pijplijnparameter zijn in ParallelRunStep in azureml.pipeline.steps
  + **azureml-pipeline-steps**
    + Door deze wijziging kan de gebruiker een optionele runconfig aan de moduleVersion verstrekken bij het aanroepen van de module. Publish_python_script.
  + **azureml-train-automl-client**
    + Er is ondersteuning voor meerdere talen voor deep learning-transformermodellen zoals ML in geautomatiseerde ML.
    + Verwijder de afgeschafte parameter lag_length uit de documentatie.
    + De documentatie voor prognoseparameters is verbeterd. De lag_length parameter is afgeschaft.
  + **azureml-train-automl-runtime**
    + Het inschakelen van uitleg voor AutoML- En Ensemble-modellen die Het Model van Het model van Het Type bevatten.
    + Documentatie-updates voor azureml-train-automl-*-pakketten.
  + **azureml-train-core**
    + Ondersteuning voor TensorFlow versie 2.1 in de PyTorch Estimator
    + Verbeteringen in het pakket azureml-train-core.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK voor Python v1.6.0

+ **Nieuwe functies**
  + **azureml-automl-runtime**
    + AutoML-prognoses bieden nu ondersteuning voor prognoses buiten de vooraf opgegeven max-horizon zonder het model opnieuw te trainen. Wanneer de bestemming van de prognose verder in de toekomst ligt dan de opgegeven maximumperiode, zal de functie forecast() nog steeds puntvoorspellingen naar de latere datum maken met behulp van een recursieve bewerkingsmodus. Voor de afbeelding van de nieuwe functie raadpleegt u de sectie 'Prognoses verder dan de maximumperiode' van het notebook 'forecasting-forecast-function' in [de map .](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
  
  + **azureml-pipeline-steps**
    + ParallelRunStep is nu uitgebracht en maakt deel uit van **het pakket azureml-pipeline-steps.** Bestaande ParallelRunStep in **het pakket azureml-contrib-pipeline-steps** is afgeschaft. Wijzigingen van de openbare preview-versie:
      + Optionele configureerbare parameter toegevoegd voor het bepalen van de maximale aanroep om de methode uit te voeren voor `run_max_try` een bepaalde batch, de standaardwaarde is 3.
      + Er worden geen PipelineParameters meer automatisch gegenereerd. De volgende configureerbare waarden kunnen expliciet worden ingesteld als PipelineParameter.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + De standaardwaarde voor process_count_per_node is gewijzigd in 1. De gebruiker moet deze waarde afstemmen voor betere prestaties. De best practice is om in te stellen als het aantal GPU- of CPU-knooppunt.
      + ParallelRunStep injecteert geen pakketten. De gebruiker moet **azureml-core-** en **azureml-dataprep[pandas, fuse]** pakketten opnemen in de omgevingsdefinitie. Als er een aangepaste Docker-installatie afbeelding wordt gebruikt user_managed_dependencies moet de gebruiker conda op de installatier installeren.
      
+ **Wijzigingen die fouten veroorzaken**
  + **azureml-pipeline-steps**
    + Het gebruik van azureml.dprep.Dataflow is afgeschaft als een geldig type invoer voor AutoMLConfig
  + **azureml-train-automl-client**
    + Het gebruik van azureml.dprep.Dataflow is afgeschaft als een geldig type invoer voor AutoMLConfig

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + De fout opgelost waarbij een waarschuwing kan worden afgedrukt tijdens de vraag `get_output` van de gebruiker om de client te downgraden.
    + Mac bijgewerkt om te vertrouwen op cudatoolkit=9.0 omdat deze nog niet beschikbaar is op versie 10.
    + Het opheffen van beperkingen voor het model voor het maken van xgboost-modellen wanneer deze zijn getraind op externe rekenkracht.
    + Verbeterde logboekregistratie in AutoML
    + De foutafhandeling voor aangepaste featurization in prognosetaken is verbeterd.
    + Er is functionaliteit toegevoegd waarmee gebruikers functies met een achterstand kunnen opnemen om prognoses te genereren.
    + Updates voor foutbericht om gebruikersfout correct weer te geven.
    + Ondersteuning voor cv_split_column_names worden gebruikt met training_data
    + Werk de logboekregistratie van het uitzonderingsbericht en traceback bij.
  + **azureml-automl-runtime**
    + Schakel beveiligingsrails in voor het voorspellen van ontbrekende waarde-imputaties.
    + Verbeterde logboekregistratie in AutoML
    + Fijnafhandeling van foutafhandeling toegevoegd voor uitzonderingen voor gegevensvoorbereiding
    + Het verwijderen van beperkingen voor phrophet- en xgboost-modellen wanneer deze zijn getraind op externe rekenkracht.
    + `azureml-train-automl-runtime` en `azureml-automl-runtime` hebben bijgewerkte afhankelijkheden voor `pytorch` , en `scipy` `cudatoolkit` . we ondersteunen `pytorch==1.4.0` nu , en `scipy>=1.0.0,<=1.3.1` `cudatoolkit==10.1.243` .
    + De foutafhandeling voor aangepaste featurization in prognosetaken is verbeterd.
    + Het mechanisme voor frequentiedetectie van gegevenssets voor prognoses is verbeterd.
    + Er is een probleem opgelost met het trainen van het Model voor Het model van Het model voor bepaalde gegevenssets.
    + De automatische detectie van max horizon tijdens de prognose is verbeterd.
    + Er is functionaliteit toegevoegd om gebruikers in staat te stellen functies met een achterstand op te nemen om prognoses te genereren.
    +  Voegt functionaliteit toe aan de prognosefunctie om het mogelijk te maken prognoses buiten de getrainde horizon te bieden zonder het prognosemodel opnieuw te trainen.
    + Ondersteuning voor cv_split_column_names worden gebruikt met training_data
  + **azureml-contrib-automl-dnn-forecasting**
    + Verbeterde logboekregistratie in AutoML
  + **azureml-contrib-platform**
    + Ondersteuning toegevoegd voor Windows-services in ManagedInferencing
    + OudeEMENT-werkstromen verwijderen, zoals attachING compute, SingleModelMirWebservice class - Clean out model profiling placed in contrib-package
  + **azureml-contrib-pipeline-steps**
    + Kleine oplossing voor YAML-ondersteuning
    + ParallelRunStep wordt vrijgegeven voor algemene beschikbaarheid- azureml.contrib.pipeline.steps heeft een kennisgeving over afschaffing en wordt verplaatst naar azureml.pipeline.steps
  + **azureml-contrib-reinforcementlearning**
    + Hulpprogramma voor belastingstests voor RL
    + RL-estimator heeft slimme standaardinstellingen
  + **azureml-core**
    + OudeEMENT-werkstromen verwijderen, zoals attachING compute, SingleModelMirWebservice class - Clean out model profiling placed in contrib-package
    + De informatie die aan de gebruiker is verstrekt in het geval van een mislukte profilering is opgelost: de aanvraag-id is opgenomen en het bericht opnieuw geformuleerd om zinvoller te zijn. Nieuwe profileringswerkstroom toegevoegd aan profileringsprofielen
    + Verbeterde fouttekst in het geval van mislukte uitvoeringen van gegevenssets.
    + Cli-ondersteuning voor private link voor werkruimte toegevoegd.
    + Er is een optionele parameter toegevoegd aan waarmee u kunt opgeven hoe regels moeten worden verwerkt `invalid_lines` `Dataset.Tabular.from_json_lines_files` die ongeldige JSON bevatten.
    + In de volgende release wordt het maken van rekenkracht op basis van run-based afgeschaft. We raden u aan een echt Amlcompute-cluster te maken als een permanent rekendoel en de clusternaam te gebruiken als het rekendoel in uw uitvoeringsconfiguratie. Bekijk hier een voorbeeldnote notebook: aka.ms/amlcomputenb
    + Verbeterde foutberichten in het geval van mislukte uitvoeringen van gegevenssets.
  + **azureml-dataprep**
    + Waarschuwing gemaakt om pyarrow-versie explicieter bij te voeren.
    + Verbeterde foutafhandeling en het geretourneerde bericht in het geval dat de gegevensstroom niet kan worden uitgevoerd.
  + **azureml-interpret**
    + Documentatie-updates voor het pakket azureml-interpret.
    + Probleem opgelost met interpreteerbaarheidspakketten en notebooks om compatibel te zijn met de nieuwste sklearn-update
  + **azureml-opendatasets**
    + geen retourneren wanneer er geen gegevens worden geretourneerd.
    + De prestaties van to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Snelle oplossing voor ParallelRunStep waarbij het laden van YAML is verbroken
    + ParallelRunStep wordt vrijgegeven voor algemene beschikbaarheid- azureml.contrib.pipeline.steps heeft een kennisgeving over afschaffing en wordt verplaatst naar azureml.pipeline.steps- nieuwe functies zijn onder andere: 1. Gegevenssets als PipelineParameter 2. Nieuwe parameter run_max_retry 3. Configureerbare naam append_row uitvoerbestand
  + **azureml-pipeline-steps**
    + Azureml.dprep.Dataflow is afgeschaft als een geldig type voor invoergegevens.
    + Snelle oplossing voor ParallelRunStep waarbij het laden van YAML is verbroken
    + ParallelRunStep wordt vrijgegeven voor algemene beschikbaarheid- azureml.contrib.pipeline.steps heeft een afschaffingsbericht en wordt verplaatst naar azureml.pipeline.steps- nieuwe functies zijn onder andere:
      + Gegevenssets als PipelineParameter
      + Nieuwe parameter run_max_retry
      + Configureerbare naam append_row uitvoerbestand
  + **azureml-telemetry**
    + Werk de logboekregistratie van het uitzonderingsbericht en traceback bij.
  + **azureml-train-automl-client**
    + Verbeterde logboekregistratie in AutoML
    + Updates voor foutbericht om gebruikersfout correct weer te geven.
    + Ondersteuning voor cv_split_column_names worden gebruikt met training_data
    + Azureml.dprep.Dataflow is afgeschaft als een geldig type voor invoergegevens.
    + Mac bijgewerkt om te vertrouwen op cudatoolkit=9.0 omdat deze nog niet beschikbaar is op versie 10.
    + Beperkingen voor phrophet- en xgboost-modellen verwijderen wanneer ze zijn getraind op externe rekenkracht.
    + `azureml-train-automl-runtime` en `azureml-automl-runtime` hebben bijgewerkte afhankelijkheden `pytorch` voor , en `scipy` `cudatoolkit` . we ondersteunen nu `pytorch==1.4.0` `scipy>=1.0.0,<=1.3.1` , en `cudatoolkit==10.1.243` .
    + Er is functionaliteit toegevoegd waarmee gebruikers functies met een achterstand kunnen opnemen om prognoses te genereren.
  + **azureml-train-automl-runtime**
    + Verbeterde logboekregistratie in AutoML
    + Fijnafhandeling van foutafhandeling toegevoegd voor uitzonderingen voor gegevensvoorbereiding
    + Beperkingen voor phrophet- en xgboost-modellen verwijderen wanneer ze zijn getraind op externe rekenkracht.
    + `azureml-train-automl-runtime` en `azureml-automl-runtime` hebben bijgewerkte afhankelijkheden `pytorch` voor , en `scipy` `cudatoolkit` . we ondersteunen nu `pytorch==1.4.0` `scipy>=1.0.0,<=1.3.1` , en `cudatoolkit==10.1.243` .
    + Updates voor foutbericht om de gebruikersfout correct weer te geven.
    + Ondersteuning voor cv_split_column_names worden gebruikt met training_data
  + **azureml-train-core**
    + Er is een nieuwe set specifieke HyperDrive-uitzonderingen toegevoegd. azureml.train.hyperdrive geeft nu gedetailleerde uitzonderingen weer.
  + **azureml-widgets**
    + AzureML-widgets worden niet weergegeven in JupyterLab
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Azure Machine Learning SDK voor Python v1.5.0

+ **Nieuwe functies**
  + **Preview-functies**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning brengt preview-ondersteuning voor bekrachtigings learning uit met behulp van het [Ray-framework.](https://ray.io) De `ReinforcementLearningEstimator` maakt het mogelijk om agents voor bekrachtigingstraining te trainen voor GPU- en CPU-rekendoelen in Azure Machine Learning.

+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + Herstelt een per ongeluk achtergelaten waarschuwingslogboek in mijn vorige pr. Het logboek is gebruikt voor het debuggen van gegevens en is per ongeluk achter gelaten.
    + Opgeloste fout: clients informeren over gedeeltelijke fouten tijdens profilering
  + **azureml-automl-core**
    + Vereenvoudig Het AutoArima-model in AutoML-prognoses door parallelle aanpassing in te stellen voor de tijdreeks wanneer gegevenssets meerdere tijdreeksen hebben. Als u wilt profiteren van deze nieuwe functie, kunt u het beste 'max_cores_per_iteration = -1' (dat wil zeggen, met alle beschikbare CPU-kernen) instellen in AutoMLConfig.
    + KeyError voor afdrukken van beveiligingsrails in console-interface opgelost
    + Foutbericht voor experimentation_timeout_hours
    + Tensorflow-modellen voor AutoML zijn afgeschaft.
  + **azureml-automl-runtime**
    + Foutbericht voor experimentation_timeout_hours
    + Niet-geclassificeerde uitzondering opgelost bij het deserialiseren vanuit cacheopslag
    + Vereenvoudig Het AutoArima-model in AutoML-prognoses door parallelle aanpassing in te stellen voor de tijdreeks wanneer gegevenssets meerdere tijdreeksen hebben.
    + Probleem opgelost met ingeschakelde prognoses voor de gegevenssets waarbij de test-/voorspellingsset geen van de grains uit de trainingsset bevat.
    + Verbeterde verwerking van ontbrekende gegevens
    + Probleem opgelost met voorspellingsintervallen tijdens het voorspellen van gegevenssets, die tijdreeksen bevatten, die niet in tijd zijn uitgelijnd.
    + Betere validatie van de gegevensvorm toegevoegd voor de prognosetaken.
    + De frequentiedetectie is verbeterd.
    + Er is een beter foutbericht gemaakt als de kruisvalidatie voor prognosetaken niet kan worden gegenereerd.
    + Console-interface herstellen om ontbrekende waarde op de juiste manier af te drukken.
    + Het afdwingen van gegevenstypecontroles op cv_split_indices invoer in AutoMLConfig.
  + **azureml-cli-common**
    + Opgeloste fout: clients informeren over gedeeltelijke fouten tijdens profilering
  + **azureml-contrib-platform**
    + Voegt een klasse azureml.contrib.version.RevisionStatus toe waarmee informatie wordt doorgegeven over de momenteel geïmplementeerde REVISION-revisie en de meest recente versie die door de gebruiker is opgegeven. Deze klasse is opgenomen in het object Deployment_status onder het kenmerk 'deployment_status'.
    + Hiermee schakelt u updates in voor webservices van het typeSturWebservice en de onderliggende klasse SingleModelMirWebservice.
  + **azureml-contrib-reinforcementlearning**
    + Ondersteuning toegevoegd voor Ray 0.8.3
    + AmlWindowsCompute biedt alleen ondersteuning voor Azure Files als aan een opslag gemonteerde opslag
    + Naam van health_check_timeout gewijzigd in health_check_timeout_seconds
    + Er zijn enkele beschrijvingen van klassen/methoden opgelost.
  + **azureml-core**
    + Ingeschakelde WASB -> Blob-conversies in Azure Government clouds van China.
    + Er is een fout opgelost waardoor lezersrollen az ml run CLI-opdrachten kunnen gebruiken om runinformatie op te halen
    + Onnodige logboekregistratie verwijderd tijdens externe azure ML-runs met invoersets.
    + RTvnPackage ondersteunt nu de parameter 'versie' voor de CRAN-pakketversie.
    + Opgeloste fout: clients informeren over gedeeltelijke fouten tijdens profilering
    + Er is een Float-verwerking in Europese stijl toegevoegd voor azureml-core.
    + Ingeschakelde private link-functies voor werkruimten in Azure ML SDK.
    + Wanneer u een TabularDataset maakt met behulp van , kunt u opgeven of lege waarden moeten worden geladen als Geen of als lege tekenreeks door het `from_delimited_files` Booleaanse argument in te `empty_as_string` stellen.
    + Floatverwerking in Europese stijl toegevoegd voor gegevenssets.
    + Verbeterde foutberichten bij mislukte gegevenssets.
  + **azureml-datadrift**
    + De gegevensdriftresultatenquery van de SDK had een fout die geen onderscheid maakte tussen de metrische gegevens voor de minimum-, maximum- en gemiddelde functie, wat leidde tot dubbele waarden. We hebben deze fout opgelost door het voorvoegsel doel of de basislijn toe te staan aan de metrische namen. Vóór: duplicaat min, max, mean. Na: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + De verwerking van beperkte Python-schrijfomgevingen verbeteren door ervoor te zorgen dat .NET-afhankelijkheden vereist zijn voor de levering van gegevens.
    + Probleem opgelost bij het maken van een gegevensstroom in een bestand met voorlooploze records.
    + Opties voor foutafhandeling `to_partition_iterator` toegevoegd voor , vergelijkbaar met `to_pandas_dataframe` .
  + **azureml-interpret**
    + Beperkte lengtelimieten voor uitlegpaden om de kans te verkleinen dat de Windows-limiet wordt overschreden
    + Bugfix voor sparse uitleg die is gemaakt met de uitleg over nabootsen met behulp van een lineair surrogaatmodel.
  + **azureml-opendatasets**
    + Probleem opgelost waarbij de kolommen van MNIST worden geparseerd als tekenreeks, wat int moet zijn.
  + **azureml-pipeline-core**
    + Het toestaan van de optie regenerate_outputs bij het gebruik van een module die is ingesloten in een ModuleStep.
  + **azureml-train-automl-client**
    + Tensorflow-modellen voor AutoML zijn afgeschaft.
    + Probleem opgelost met het toestaan van gebruikers om niet-ondersteunde algoritmen in de lokale modus weer te geven
    + Doc-oplossingen voor AutoMLConfig.
    + Het afdwingen van gegevenstypecontroles voor cv_split_indices invoer in AutoMLConfig.
    + Er is een probleem opgelost met het mislukken van AutoML-show_output
  + **azureml-train-automl-runtime**
    + Het oplossen van een fout in Ensemble-iteraties waardoor de time-out voor het downloaden van het model niet kon worden uitgevoerd.
  + **azureml-train-core**
    + Typfout in klasse azureml.train.dnn.Nccl opgelost.
    + PyTorch versie 1.5 ondersteunen in de PyTorch Estimator
    + Los het probleem op dat de framework-afbeelding niet kan worden opgehaald in Azure Government regio bij het gebruik van estimators voor trainingskaders

  
## <a name="2020-05-04"></a>2020-05-04
**Nieuwe notebookervaring**

U kunt nu notebooks en bestanden maken, bewerken en machine learning rechtstreeks in de studio-webervaring van Azure Machine Learning. U kunt alle klassen en methoden die beschikbaar zijn in [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) vanuit deze notebooks Hier aan de [slag gebruiken](./how-to-run-jupyter-notebooks.md)

**Nieuwe functies geïntroduceerd:**

+ Verbeterde editor (Monaco-editor) die wordt gebruikt door VS Code 
+ Ui-/UX-verbeteringen
+ Celwerkbalk
+ Nieuwe notebookwerkbalk en besturingselementen voor rekenkracht
+ Statusbalk van notebook 
+ Inline kernel-schakeling
+ R-ondersteuning
+ Verbeteringen in toegankelijkheid en lokalisatie
+ Opdrachtenpalet
+ Aanvullende sneltoetsen
+ Autoopslaan
+ Verbeterde prestaties en betrouwbaarheid

Krijg toegang tot de volgende webgebaseerde ontwerphulpprogramma's vanuit de studio:
    
| Webhulpprogramma  |     Description  |
|---|---|
| Azure ML Studio Notebooks   |     Eerste in-class ontwerp voor notebookbestanden en ondersteuning voor alle beschikbare bewerking in de Azure ML Python SDK. | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK voor Python v1.4.0

+ **Nieuwe functies**
  + AmlCompute-clusters ondersteunen nu het instellen van een beheerde identiteit op het cluster op het moment van inrichten. Geef op of u een door het systeem toegewezen identiteit of een door de gebruiker toegewezen identiteit wilt gebruiken en geef een identityId door voor de laatste. Vervolgens kunt u machtigingen instellen voor toegang tot verschillende resources, zoals Storage of ACR, op een manier dat de identiteit van de berekening wordt gebruikt om veilig toegang te krijgen tot de gegevens, in plaats van een op een token gebaseerde benadering die AmlCompute momenteel gebruikt. Bekijk onze SDK-referentie voor meer informatie over de parameters.
  

+ **Wijzigingen die fouten veroorzaken**
  + AmlCompute-clusters ondersteunden een preview-functie voor het maken op basis van een run, die we over twee weken willen afbouwen. U kunt permanente rekendoelen blijven maken zoals altijd met behulp van de klasse Amlcompute, maar de specifieke benadering van het opgeven van de id 'amlcompute' als het rekendoel in run config wordt in de nabije toekomst niet ondersteund. 

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-runtime**
    + Schakel ondersteuning in voor het type Unhashable bij het berekenen van het aantal unieke waarden in een kolom.
  + **azureml-core**
    + Verbeterde stabiliteit bij het lezen van Azure Blob Storage met behulp van een TabularDataset.
    + Verbeterde documentatie voor de `grant_workspace_msi` parameter voor `Datastore.register_azure_blob_store` .
    + Er is een fout opgelost `datastore.upload` met ter ondersteuning van het argument dat eindigt op een of `src_dir` `/` `\` .
    + Er is een actie-foutbericht toegevoegd wanneer u probeert te uploaden naar een Azure Blob Storage die geen toegangssleutel of SAS-token heeft.
  + **azureml-interpret**
    + Bovengrens toegevoegd aan bestandsgrootte voor de visualisatiegegevens bij geüploade uitleg.
  + **azureml-train-automl-client**
    + Expliciet controleren op label_column_name & weight_column_name parameters voor AutoMLConfig van het type tekenreeks zijn.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep ondersteunt nu gegevensset als pijplijnparameter. De gebruiker kan een pijplijn maken met een voorbeeldgegevensset en de invoergegevensset van hetzelfde type (bestand of tabellair) wijzigen voor nieuwe pijplijn-run.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK voor Python v1.3.0

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + Er is aanvullende telemetrie toegevoegd voor bewerkingen na de training.
    + Versnelt automatische ARIMA-training met behulp van voorwaardelijke som van CSS-training (squares) voor een reeks langer dan 100. De gebruikte lengte wordt opgeslagen als de constante ARIMA_TRIGGER_CSS_TRAINING_LENGTH w/in de klasse TimeSeriesInternal op /src/azureml-automl-core/azureml/automl/core/shared/constants.py
    + De gebruikerslogboekregistratie van de prognoseuit runs is verbeterd. Nu wordt in het logboek meer informatie weergegeven over welke fase momenteel wordt uitgevoerd
    + Niet toegestaan target_rolling_window_size worden ingesteld op waarden die kleiner zijn dan 2
  + **azureml-automl-runtime**
    + Het foutbericht verbeterd dat wordt weergegeven wanneer dubbele tijdstempels worden gevonden.
    + Niet toegestaan target_rolling_window_size worden ingesteld op waarden kleiner dan 2.
    + Probleem met vertragings imputatie opgelost. Het probleem werd veroorzaakt door het onvoldoende aantal waarnemingen dat nodig is om een reeks seizoensgebonden te ontleden. De gegevens van het 'gedeseizoensgebonden' worden gebruikt om een gedeeltelijke pacf-functie (autocorrelation function) te berekenen om de vertragingsduur te bepalen.
    + Featurization-aanpassing voor kolomdoeleinden ingeschakeld voor prognosetaken door featurization config. Numeriek en categorisch als kolomdoel voor prognosetaken wordt nu ondersteund.
    + Aanpassing van drop column featurization ingeschakeld voor prognosetaken door featurization config.
    + Aanpassing van imputatie ingeschakeld voor prognosetaken door featurization config. Constante waarde-imputatie voor doelkolom en gemiddelde, mediaan, most_frequent en constante waarde-imputatie voor trainingsgegevens worden nu ondersteund.
  + **azureml-contrib-pipeline-steps**
    + Compute-namen van tekenreeksen accepteren die moeten worden doorgegeven aan ParallelRunConfig
  + **azureml-core**
    +  Environment.clone(new_name) API toegevoegd om een kopie van het Environment-object te maken
    +  Environment.docker.base_dockerfile accepteert filepath. Als een bestand kan worden opgelost, wordt de inhoud in de base_dockerfile gelezen
    + Wederzijds exclusieve waarden automatisch opnieuw instellen voor base_image en base_dockerfile wanneer de gebruiker handmatig een waarde in de ker Environment.docingesteld
    + Er user_managed toegevoegd in RSection die aangeeft of de omgeving wordt beheerd door de gebruiker of door AzureML.
    + Gegevensset: er is een fout opgelost bij het downloaden van gegevenssets als het gegevenspad unicode-tekens bevat.
    + Gegevensset: verbeterd mechanisme voor het in de opslag in de caching van gegevenssets om te voldoen aan de minimale vereiste schijfruimte in Azure Machine Learning Compute, waardoor het knooppunt niet meer kan worden gebruikt en de taak wordt geannuleerd.
    + Gegevensset: We voegen een index toe voor de tijdreekskolom wanneer u een tijdreeksgegevensset als pandas-gegevensframe gebruikt, die wordt gebruikt om de toegang tot gegevens op basis van tijdreeksen te versnellen.  Voorheen kreeg de index dezelfde naam als de tijdstempelkolom, wat verwarrend was voor gebruikers over wat de werkelijke tijdstempelkolom is en wat de index is. We geven nu geen specifieke naam aan de index omdat deze niet als kolom mag worden gebruikt. 
    + Gegevensset: Verificatieprobleem met gegevensset in onafhankelijke cloud opgelost.
    + Gegevensset: Er is een fout opgelost voor gegevenssets die zijn gemaakt op basis `Dataset.to_spark_dataframe` van Azure PostgreSQL-gegevensstores.
  + **azureml-interpret**
    + Globale scores toegevoegd aan visualisatie als de waarden voor lokaal belang verspreid zijn
    + Azureml-interpret bijgewerkt voor gebruik van interpret-community 0.9.*
    + Probleem opgelost met downloaden van uitleg met sparse evaluatiegegevens
    + Ondersteuning toegevoegd voor sparse-indeling van het uitlegobject in AutoML
  + **azureml-pipeline-core**
    + Ondersteuning voor ComputeInstance als rekendoel in pijplijnen
  + **azureml-train-automl-client**
    + Er is aanvullende telemetrie toegevoegd voor bewerkingen na de training.
    + De regressie bij vroegtijdig stoppen is opgelost
    + Azureml.dprep.Dataflow is afgeschaft als een geldig type voor invoergegevens.
    +  Het wijzigen van de standaard time-out voor autoML-experimenten in zes dagen.
  + **azureml-train-automl-runtime**
    + Er is aanvullende telemetrie toegevoegd voor bewerkingen na de training.
    + sparse AutoML end-to-end-ondersteuning toegevoegd
  + **azureml-opendatasets**
    + Aanvullende telemetrie toegevoegd voor servicemonitor.
    + De front door blob inschakelen om de stabiliteit te vergroten 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK voor Python v1.2.0

+ **Wijzigingen die fouten veroorzaken**
  + Ondersteuning voor Python 2.7 wordt niet meer ondersteund

+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + Voegt '--subscription-id' toe aan `az ml model/computetarget/service` opdrachten in de CLI
    + Ondersteuning toegevoegd voor het doorgeven van door de klant beheerde sleutel (CMK) vault_url, key_name en key_version voor ACI-implementatie
  + **azureml-automl-core** 
    + Aangepaste imputatie met constante waarde ingeschakeld voor zowel X- als y-gegevensvoorspellingstaken.
    + Het probleem in is opgelost met het weergeven van foutberichten aan de gebruiker.    
  + **azureml-automl-runtime**
    + Het probleem in opgelost met prognoses voor de gegevenssets, met grains met slechts één rij
    + De hoeveelheid geheugen verlaagd die nodig is voor de prognosetaken.
    + Er zijn betere foutberichten toegevoegd als de tijdkolom een onjuiste indeling heeft.
    + Aangepaste imputatie met constante waarde ingeschakeld voor zowel X- als y-gegevensvoorspellingstaken.
  + **azureml-core**
    + Ondersteuning toegevoegd voor het laden van ServicePrincipal vanuit omgevingsvariabelen: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID en AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Er is een nieuwe parameter geïntroduceerd in : Standaard ( ) worden alle regelbreaks, inclusief die in waarden tussen aangehaalde velden, geïnterpreteerd `support_multi_line` `Dataset.Tabular.from_delimited_files` als een `support_multi_line=False` record-onderbreking. Het lezen van gegevens op deze manier is sneller en geoptimaliseerd voor parallelle uitvoering op meerdere CPU-kernen. Dit kan er echter toe leiden dat er op de stille manier meer records worden geproduceerd met verkeerd uitgelijnde veldwaarden. Dit moet worden ingesteld op wanneer bekend is dat de bestanden met scheidingstekens `True` regel onderbrekingen bevatten.
    + De mogelijkheid toegevoegd om ADLS Gen2 registreren in de Azure Machine Learning CLI
    + De naam van parameter 'fine_grain_timestamp' gewijzigd in 'timestamp' en parameter 'coarse_grain_timestamp' in 'partition_timestamp' voor de methode with_timestamp_columns() in TabularDataset om het gebruik van de parameters beter weer te geven.
    + De maximale lengte van de experimentnaam is verhoogd tot 255.
  + **azureml-interpret**
    + Azureml-interpret bijgewerkt voor interpret-community 0.7.*
  + **azureml-sdk**
    + Het wijzigen van afhankelijkheden met compatibele versie Tilde voor de ondersteuning van patching in pre-release en stabiele releases.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK voor Python v1.1.5

+ **Afschaffing van functies**
  + **Python 2.7**
    + Laatste versie ter ondersteuning van Python 2.7

+ **Wijzigingen die fouten veroorzaken**
  + **Semantic Versioning 2.0.0**
    + Vanaf versie 1.1 wordt Semantic Versioning 2.0.0 door De Python SDK van Azure ML gebruikt. [Lees hier meer.](https://semver.org/) Alle volgende versies volgen een nieuw nummerschema en semantisch versiecontract. 

+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + Wijzig de naam van de CLI-eindpuntopdracht van az ml endpoint aks in 'az ml endpoint real time' voor consistentie.
    + CLI-installatie-instructies bijwerken voor stabiele en experimentele vertakking CLI
    + Profilering van één exemplaar is opgelost om een aanbeveling te produceren en is beschikbaar gesteld in de core-SDK.
  + **azureml-automl-core**
    + De deferentie in de Batch-modus is ingeschakeld (waarbij meerdere rijen één keer worden genomen) voor AutoML ONNX-modellen
    + Verbeterde detectie van frequentie op de gegevenssets, zonder gegevens of met onregelmatige gegevenspunten
    + De mogelijkheid toegevoegd om gegevenspunten te verwijderen die niet voldoen aan de dominante frequentie.
    + De invoer van de constructor is gewijzigd om een lijst met opties te maken voor het toepassen van de imputatieopties voor de bijbehorende kolommen.
    + De logboekregistratie van fouten is verbeterd.
  + **azureml-automl-runtime**
    + Het probleem opgelost met de fout die is opgetreden als het grain niet aanwezig was in de trainingsset die in de testset werd weergegeven
    + De vereiste y_query verwijderd tijdens het scoren van de prognoseservice
    + Het probleem met prognoses opgelost wanneer de gegevensset korte grains met lange tijdshiaten bevat.
    + Het probleem opgelost wanneer de auto max horizon is ingeschakeld en de datumkolom datums bevat in de vorm van tekenreeksen. Er zijn juiste conversie- en foutberichten toegevoegd voor wanneer conversie naar datum niet mogelijk is
    + Systeemeigen NumPy en SciPy gebruiken voor het serialiseren en deserialiseren van tussenliggende gegevens voor FileCacheStore (gebruikt voor lokale AutoML-runs)
    + Er is een fout opgelost waarbij mislukte onderliggende runs vast konden komen te zitten in de status Wordt uitgevoerd.
    + Hogere snelheid van featurization.
    + De frequentiecontrole tijdens het scoren is opgelost. Voor de prognosetaken is nu geen strikte frequentie-equivalentie tussen trainen en testset vereist.
    + De invoer van de constructor is gewijzigd om een lijst met opties te maken voor het toepassen van de imputatieopties voor de bijbehorende kolommen.
    + Fouten met betrekking tot selectie van vertragingstype opgelost.
    + De niet-geclassificeerde fout opgelost die op de gegevenssets werd veroorzaakt, met grains met één rij
    + Het probleem met de traagheid van frequentiedetectie is opgelost.
    + Lost een fout in de afhandeling van AutoML-uitzonderingen op die de werkelijke reden heeft veroorzaakt dat de trainingsfout is vervangen door een AttributeError.
  + **azureml-cli-common**
    + Profilering van één exemplaar is opgelost om een aanbeveling te produceren en is beschikbaar gesteld in de core-SDK.
  + **azureml-contrib-platform**
    + Voegt functionaliteit toe in de klasseFunctionaliteitWebservice om het toegangs token op te halen
    + Token-auth standaard gebruiken voor DenWebservice tijdens aanroepEnWebservice.run() - Alleen vernieuwen als de aanroep mislukt
    + Voor de implementatie van de Mobiele webservice zijn nu respectievelijk de juiste SKU's [Standard_DS2_v2, Standard_F16, Standard_A2_v2] in plaats van [Ds2v2, A2v2 en F16] vereist.
  + **azureml-contrib-pipeline-steps**
    + Optionele parameter side_inputs toegevoegd aan ParallelRunStep. Deze parameter kan worden gebruikt om een map aan de container te plaatsen. Momenteel worden datareference en PipelineData ondersteund.
    + Parameters die in ParallelRunConfig worden doorgegeven, kunnen nu worden overschreven door pijplijnparameters door te geven. Nieuwe pijplijnparameters die aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count en aml_process_count_per_node maken al deel uit van een eerdere versie).
  + **azureml-core**
    + Geïmplementeerde AzureML-webservices worden nu standaard ingesteld op `INFO` logboekregistratie. Dit kan worden beheerd door de `AZUREML_LOG_LEVEL` omgevingsvariabele in te stellen in de geïmplementeerde service.
    + Python SDK maakt gebruik van discovery-service om het api-eindpunt te gebruiken in plaats van pijplijnen.
    + Verwissel naar de nieuwe routes in alle SDK-aanroepen.
    + De routering van aanroepen naar de ModelManagementService is gewijzigd in een nieuwe uniforme structuur.
      + Updatemethode voor werkruimte openbaar beschikbaar gemaakt.
      + Er image_build_compute parameter toegevoegd aan de updatemethode van de werkruimte, zodat de gebruiker de berekening voor het bouwen van de afbeelding kan bijwerken.
    + Afschaffingsberichten toegevoegd aan de oude profileringswerkstroom. Cpu- en geheugenlimieten voor profilering opgelost.
    + RSection toegevoegd als onderdeel van Omgeving om R-taken uit te voeren.
    + Validatie toegevoegd `Dataset.mount` aan om een fout te geven wanneer de bron van de gegevensset niet toegankelijk is of geen gegevens bevat.
    + Toegevoegd als een extra parameter voor de Datastore CLI voor het registreren van Een Azure Blob-container waarmee u een blobcontainer kunt registreren die `--grant-workspace-msi-access` zich achter een VNet begeeft.
    + Profilering van één exemplaar is opgelost om een aanbeveling te produceren en is beschikbaar gesteld in de core-SDK.
    + Het probleem in de aks.py _deploy.
    + Valideert de integriteit van modellen die worden geüpload om stille opslagfouten te voorkomen.
    + De gebruiker kan nu een waarde voor de auth-sleutel opgeven bij het opnieuw instellen van sleutels voor webservices.
    + Er is een fout opgelost waarbij hoofdletters niet kunnen worden gebruikt als invoernaam van de gegevensset.
  + **azureml-defaults**
    + `azureml-dataprep` wordt nu geïnstalleerd als onderdeel van `azureml-defaults` . Het is niet langer nodig om gegevensvoorbereiding [fuse] handmatig te installeren op rekendoelen om gegevenssets te kunnen aan elkaar te plaatsen.
  + **azureml-interpret**
    + Azureml-interpret bijgewerkt voor interpret-community 0.6.*
    + Azureml-interpret bijgewerkt om afhankelijk te zijn van interpret-community 0.5.0
    + Azureml-style-uitzonderingen toegevoegd aan azureml-interpret
    + Er is een probleem opgelost met DeepScoringExplainer-serialisatie voor Keras-modellen
  + **azureml-mlflow**
    + Ondersteuning voor onafhankelijke clouds toevoegen aan azureml.mlflow
  + **azureml-pipeline-core**
    + Notebook voor batchscore van pijplijn maakt nu gebruik van ParallelRunStep
    + Er is een fout opgelost waarbij PythonScriptStep-resultaten onjuist konden worden hergebruikt ondanks het wijzigen van de argumentenlijst
    + De mogelijkheid toegevoegd om het type kolommen in te stellen bij het aanroepen van de parse_*-methoden op `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + De is `AutoMLStep` naar het `azureml-pipeline-steps` pakket verplaatst. De in is `AutoMLStep` `azureml-train-automl-runtime` afgeschaft.
    + Documentatievoorbeeld toegevoegd voor gegevensset als PythonScriptStep-invoer
  + **azureml-tensorboard**
    + azureml-tensorboard bijgewerkt ter ondersteuning van tensorflow 2.0
    + Het juiste poortnummer tonen bij het gebruik van een aangepaste Tensorboard-poort op een reken-exemplaar
  + **azureml-train-automl-client**
    + Er is een probleem opgelost waarbij bepaalde pakketten kunnen worden geïnstalleerd bij onjuiste versies op externe uitvoeringen.
    + Probleem met het overschrijven van FeaturizationConfig opgelost dat aangepaste featurization-configuratie filtert.
  + **azureml-train-automl-runtime**
    + Het probleem met frequentiedetectie in de externe runs is opgelost
    + De `AutoMLStep` in het `azureml-pipeline-steps` pakket verplaatst. De in is `AutoMLStep` `azureml-train-automl-runtime` afgeschaft.
  + **azureml-train-core**
    + PyTorch-versie 1.4 ondersteunen in de PyTorch-estimator
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK voor Python v1.1.2rc0 (pre-release)

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + De deferentie in de Batch-modus is ingeschakeld (waarbij meerdere rijen één keer worden genomen) voor AutoML ONNX-modellen
    + Verbeterde detectie van frequentie op de gegevenssets, zonder gegevens of met onregelmatige gegevenspunten
    + De mogelijkheid toegevoegd om gegevenspunten te verwijderen die niet voldoen aan de dominante frequentie.
  + **azureml-automl-runtime**
    + Probleem opgelost met de fout die is opgetreden als de grain niet aanwezig was in de trainingsset die in de testset werd weergegeven
    + De vereiste y_query verwijderd tijdens het scoren op de prognoseservice
  + **azureml-contrib-platform**
    + Voegt functionaliteit toe in de klasseFunctionaliteitWebservice om het toegangs token op te halen
  + **azureml-core**
    + Geïmplementeerde AzureML-webservices worden nu standaard ingesteld op `INFO` logboekregistratie. Dit kan worden beheerd door de `AZUREML_LOG_LEVEL` omgevingsvariabele in te stellen in de geïmplementeerde service.
    + Probleem opgelost bij het `Dataset.get_all` retourneren van alle gegevenssets die zijn geregistreerd bij de werkruimte.
    + Foutbericht verbeteren wanneer ongeldig type wordt doorgegeven aan argument van API's voor het maken `path` van gegevenssets.
    + Python SDK maakt gebruik van de discovery-service om het api-eindpunt te gebruiken in plaats van pijplijnen.
    + Wisselen naar de nieuwe routes in alle SDK-aanroepen
    + De routering van aanroepen naar de ModelManagementService wordt gewijzigd in een nieuwe uniforme structuur
      + De updatemethode voor de werkruimte is openbaar beschikbaar gemaakt.
      + Er is image_build_compute parameter toegevoegd aan de updatemethode van de werkruimte, zodat de gebruiker de berekening voor het bouwen van de afbeelding kan bijwerken
    +  Afschaffingsberichten toegevoegd aan de oude profileringswerkstroom. Cpu- en geheugenlimieten voor profilering opgelost
  + **azureml-interpret**
    + update azureml-interpret to interpret-community 0.6.*
  + **azureml-mlflow**
    + Ondersteuning voor onafhankelijke clouds toevoegen aan azureml.mlflow
  + **azureml-pipeline-steps**
    + De is `AutoMLStep` verplaatst naar `azureml-pipeline-steps package` de . De in is `AutoMLStep` `azureml-train-automl-runtime` afgeschaft.
  + **azureml-train-automl-client**
    + Er is een probleem opgelost waarbij bepaalde pakketten op onjuiste versies op externe uitvoeringen kunnen worden geïnstalleerd.
  + **azureml-train-automl-runtime**
    + Het probleem met frequentiedetectie in de externe runs is opgelost
    + De is `AutoMLStep` verplaatst naar `azureml-pipeline-steps package` de . De in is `AutoMLStep` `azureml-train-automl-runtime` afgeschaft.
  + **azureml-train-core**
    + De is `AutoMLStep` verplaatst naar `azureml-pipeline-steps package` de . De in is `AutoMLStep` `azureml-train-automl-runtime` afgeschaft.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK voor Python v1.1.1rc0 (pre-release)

+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + Profilering van één exemplaar is opgelost om een aanbeveling te produceren en is beschikbaar gesteld in de core-SDK.
  + **azureml-automl-core**
    + De logboekregistratie van fouten is verbeterd.
  + **azureml-automl-runtime**
    + Het probleem met prognoses opgelost wanneer de gegevensset korte grains met lange hiaten bevat.
    + Het probleem opgelost wanneer de auto max horizon is ingeschakeld en de datumkolom datums bevat in de vorm van tekenreeksen. We hebben de juiste conversie en logische fout toegevoegd als conversie naar datum niet mogelijk is
    + Systeemeigen NumPy en SciPy gebruiken voor het serialiseren en deserialiseren van tussenliggende gegevens voor FileCacheStore (gebruikt voor lokale AutoML-runs)
    + Er is een fout opgelost waarbij mislukte onderliggende runs vast konden komen te zitten in de status Actief.
  + **azureml-cli-common**
    + Profilering van één exemplaar is opgelost om een aanbeveling te produceren en is beschikbaar gesteld in de core-SDK.
  + **azureml-core**
    + Toegevoegd als een extra parameter voor de Datastore CLI voor het registreren van Een Azure Blob-container waarmee u een blobcontainer kunt registreren die `--grant-workspace-msi-access` zich achter een VNet
    + Profilering van één exemplaar is opgelost om een aanbeveling te produceren en is beschikbaar gesteld in de core-SDK.
    + Het probleem in aks.py _deploy
    + Valideert de integriteit van modellen die worden geüpload om fouten met stille opslag te voorkomen.
  + **azureml-interpret**
    + azureml-style exceptions toegevoegd aan azureml-interpret
    + Er is een probleem opgelost met DeepScoringExplainer-serialisatie voor Keras-modellen
  + **azureml-pipeline-core**
    + Notebook voor batchscore van pijplijn maakt nu gebruik van ParallelRunStep
  + **azureml-pipeline-steps**
    + De `AutoMLStep` in het `azureml-pipeline-steps` pakket verplaatst. De in is `AutoMLStep` `azureml-train-automl-runtime` afgeschaft.
  + **azureml-contrib-pipeline-steps**
    + Optionele parameter side_inputs toegevoegd aan ParallelRunStep. Deze parameter kan worden gebruikt om de map aan de container te mounten. Momenteel worden de typen DataReference en PipelineData ondersteund.
  + **azureml-tensorboard**
    + azureml-tensorboard bijgewerkt ter ondersteuning van tensorflow 2.0
  + **azureml-train-automl-client**
    + Probleem met het overschrijven van FeaturizationConfig opgelost dat aangepaste featurization-configuratie filtert.
  + **azureml-train-automl-runtime**
    + De `AutoMLStep` is in het `azureml-pipeline-steps` pakket verplaatst. De in is `AutoMLStep` `azureml-train-automl-runtime` afgeschaft.
  + **azureml-train-core**
    + Ondersteuning voor PyTorch versie 1.4 in de PyTorch Estimator
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK voor Python v1.1.0rc0 (pre-release)

+ **Wijzigingen die fouten veroorzaken**
  + **Semantic Versioning 2.0.0**
    + Vanaf versie 1.1 wordt Semantic Versioning 2.0.0 door De Python SDK van Azure ML gebruikt. [Lees hier meer.](https://semver.org/) Alle volgende versies volgen een nieuw nummerschema en een semantic versioning-contract. 
  
+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-runtime**
    + Hogere snelheid van featurization.
    + De frequentiecontrole tijdens het scoren is opgelost. In de prognosetaken is geen strikte frequentie-equivalentie tussen trainen en testset vereist.
  + **azureml-core**
    + De gebruiker kan nu een waarde voor de auth-sleutel opgeven bij het opnieuw instellen van sleutels voor webservices.
  + **azureml-interpret**
    + Azureml-interpret bijgewerkt om afhankelijk te zijn van interpret-community 0.5.0
  + **azureml-pipeline-core**
    + Er is een fout opgelost waarbij PythonScriptStep-resultaten onjuist konden worden hergebruikt ondanks het wijzigen van de argumentenlijst
  + **azureml-pipeline-steps**
    + Voorbeeld van documentatie toegevoegd voor gegevensset als PythonScriptStapinvoer
  + **azureml-contrib-pipeline-steps**
    + Parameters die in ParallelRunConfig worden doorgegeven, kunnen nu worden overschreven door pijplijnparameters door te geven. Nieuwe pijplijnparameters die aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count en aml_process_count_per_node maken al deel uit van een eerdere versie).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK voor Python v1.0.85

+ **Nieuwe functies**
  + **azureml-core**
    + De huidige limiet voor kerngebruik en quotum voor AmlCompute-resources in een bepaalde werkruimte en een bepaald abonnement op te halen
  
  + **azureml-contrib-pipeline-steps**
    + Gebruiker in staat stellen om gegevensset in tabelvorm door te geven als tussenliggend resultaat uit de vorige stap naar parallelrunstep

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-runtime**
    + De vereiste voor het y_query verwijderd uit de aanvraag voor de geïmplementeerde prognoseservice. 
    + De 'y_query' is verwijderd uit de sectie OrangeSap-notebookserviceaanvraag vanVakk.
    + Er is een fout opgelost waardoor er geen prognoses werden gemaakt voor de geïmplementeerde modellen, die werden gebruikt voor gegevenssets met datum/tijd-kolommen.
    + Matthews Correlation Coefficient is toegevoegd als classificatie metrische waarde voor zowel binaire als multiclass-classificatie.
  + **azureml-contrib-interpret**
    + Tekstuitleg is verwijderd uit azureml-contrib-interpret omdat tekstuitleg is verplaatst naar de interpret-text-repo die binnenkort wordt uitgebracht.
  + **azureml-core**
    + Gegevensset: het gebruik van bestandsgegevenssets is niet langer afhankelijk van numpy en pandas die moeten worden geïnstalleerd in python env.
    + De LocalWebservice.wait_for_deployment() is gewijzigd om de status van de lokale Docker-container te controleren voordat het status-eindpunt werd gepingd, waardoor het melden van een mislukte implementatie veel minder tijd kost.
    + De initialisatie van een interne eigenschap die wordt gebruikt in LocalWebservice.reload() is opgelost wanneer het serviceobject wordt gemaakt op basis van een bestaande implementatie met behulp van de constructor LocalWebservice().
    + Bewerkt foutbericht ter verduidelijking.
    + Er is een nieuwe methode met de naam get_access_token() toegevoegd aan AksWebservice die het AksServiceAccessToken-object retourneert, dat toegangstoken, vernieuwing na tijdstempel, verlooptijdstempel en tokentype bevat. 
    + De bestaande methode get_token() in AksWebservice is afgeschaft omdat de nieuwe methode alle informatie retourneert die deze methode retourneert.
    + Uitvoer gewijzigd van de opdracht az ml service get-access-token. Token hernoemd in accessToken en refreshBy om te vernieuwenNa. Eigenschappen expiryOn en tokenType toegevoegd.
    + Probleem met get_active_runs
  + **azureml-explain-model**
    + shap bijgewerkt naar 0.33.0 en interpret-community naar 0.4.*
  + **azureml-interpret**
    + shap bijgewerkt naar 0.33.0 en interpret-community naar 0.4.*
  + **azureml-train-automl-runtime**
    + Matthews Correlation Coefficient toegevoegd als classificatie metrische waarde, voor zowel binaire als multiclass classificatie.
    + Voorverwerkingsvlag voor code wordt afgeschaft en vervangen door featurization -featurization is standaard ingeschakeld

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK voor Python v1.0.83

+ **Nieuwe functies**
  + Gegevensset: Voeg twee opties toe en voor om `on_error` `out_of_range_datetime` te `to_pandas_dataframe` mislukken wanneer gegevens foutwaarden bevatten in plaats van ze in te vullen met `None` .
  + Werkruimte: De vlag is toegevoegd voor werkruimten met gevoelige gegevens waarmee verdere versleuteling mogelijk is en geavanceerde diagnostische gegevens `hbi_workspace` voor werkruimten worden uitgeschakeld. We hebben ook ondersteuning toegevoegd voor het ophalen van uw eigen sleutels voor het bijbehorende Cosmos DB-exemplaar, door de parameters en op te geven bij het maken van een werkruimte, waarmee een Cosmos DB-exemplaar in uw abonnement wordt gemaakt tijdens het inrichten van uw `cmk_keyvault` `resource_cmk_uri` werkruimte. [Lees hier meer.](./concept-data-encryption.md#azure-cosmos-db)

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-runtime**
    + Er is een regressie opgelost waardoor een TypeError werd verhoogd bij het uitvoeren van AutoML in Python-versies lager dan 3.5.4.
  + **azureml-core**
    + Opgeloste fout in `datastore.upload_files` was een relatief pad dat niet kon worden gebruikt. `./`
    + Afschaffingsberichten toegevoegd voor alle codepaden van de afbeeldingsklasse
    + Er is Modelbeheer URL-constructie voor de Azure China 21Vianet opgelost.
    + Er is een probleem opgelost waarbij modellen source_dir niet konden worden verpakt voor Azure Functions.    
    + Er is een optie toegevoegd om [Environment.build_local() te gebruiken om](/python/api/azureml-core/azureml.core.environment.environment) een afbeelding naar het containerregister van de AzureML-werkruimte te pushen
    + De SDK is bijgewerkt voor het gebruik van nieuwe tokenbibliotheek in Azure Synapse op een back-compatibele manier.
  + **azureml-interpret**
    + Er is een fout opgelost waarbij Geen werd geretourneerd toen er geen uitleg beschikbaar was om te downloaden. Er wordt nu een uitzondering gemaakt, met overeenkomend gedrag elders.
  + **azureml-pipeline-steps**
    + Het doorgeven van `DatasetConsumptionConfig` s aan de parameter wordt niet `Estimator` `inputs` toegestaan wanneer de wordt gebruikt in `Estimator` een `EstimatorStep` .
  + **azureml-sdk**
    + AutoML-client toegevoegd aan azureml-sdk-pakket, waardoor externe AutoML-runs kunnen worden verzonden zonder het volledige AutoML-pakket te installeren.
  + **azureml-train-automl-client**
    + Uitlijning gecorrigeerd op console-uitvoer voor AutoML-runs
    + Er is een fout opgelost waarbij een onjuiste versie van pandas kan worden geïnstalleerd op externe amlcompute.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK voor Python v1.0.81

+ **Opgeloste fouten en verbeteringen**
  + **azureml-contrib-interpret**
    + shap-afhankelijkheid uitstellen voor interpret-community van azureml-interpret
  + **azureml-core**
    + Rekendoel kan nu worden opgegeven als een parameter voor de bijbehorende implementatie-configuratieobjecten. Dit is met name de naam van het rekendoel dat moet worden geïmplementeerd in , niet het SDK-object.
    + CreatedBy-informatie toegevoegd aan model- en serviceobjecten. Kan worden gebruikt through.created_by
    + Er is een probleem opgelost met ContainerImage.run(), dat de HTTP-poort van de Docker-container niet juist instelde.
    + Optioneel `azureml-dataprep` maken voor `az ml dataset register` CLI-opdracht
    + Er is een fout opgelost `TabularDataset.to_pandas_dataframe` waarbij ten onrechte zou terugvallen op een alternatieve lezer en een waarschuwing zou worden afgedrukt.
  + **azureml-explain-model**
    + shap-afhankelijkheid uitstellen voor interpret-community van azureml-interpret
  + **azureml-pipeline-core**
    + Nieuwe pijplijnstap toegevoegd `NotebookRunnerStep` om een lokaal notebook uit te voeren als een stap in de pijplijn.
    + Afgeschafte get_all verwijderd voor PublishedPipelines, Schedules en PipelineEndpoints
  + **azureml-train-automl-client**
    + Afschaffing van de data_script als invoer voor AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK voor Python v1.0.79

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + FeaturizationConfig is verwijderd om te worden geregistreerd
      + Logboekregistratie bijgewerkt om alleen 'automatisch'/'uit'/'aangepast' aan te melden.
  + **azureml-automl-runtime**
    + Ondersteuning toegevoegd voor pandas. Series en pandas. Categorisch voor het detecteren van kolomgegevenstype. Voorheen werd alleen numpy.ndarray ondersteund
      + Gerelateerde codewijzigingen toegevoegd om categorische dtype correct te verwerken.
    + De interface voor de prognosefunctie is verbeterd: de parameter y_pred is optioneel gemaakt. -De docstrings zijn verbeterd.
  + **azureml-contrib-dataset**
    + Er is een fout opgelost waarbij gelabelde gegevenssets niet konden worden bevestigd.
  + **azureml-core**
    + Opgeloste fout voor `Environment.from_existing_conda_environment(name, conda_environment_name)` . De gebruiker kan een omgevings exemplaar maken dat exacte replica van de lokale omgeving is
    + De tijdreeksgerelateerde gegevenssetmethoden zijn standaard `include_boundary=True` gewijzigd in .
  + **azureml-train-automl-client**
    + Er is een probleem opgelost waarbij validatieresultaten niet worden afgedrukt wanneer de uitvoer van de show is ingesteld op false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK voor Python v1.0.76

+ **Wijzigingen die fouten veroorzaken**
  + Upgradeproblemen met Azureml-Train-AutoML
    + Een upgrade naar azureml-train-automl>=1.0.76 van azureml-train-automl<1.0.76 kan leiden tot gedeeltelijke installaties, waardoor sommige AutoML-importen mislukken. U kunt dit oplossen door het installatiescript uit te voeren dat u kunt vinden op https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd . Als u pip rechtstreeks gebruikt, kunt u het volgende doen:
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + of u kunt de oude versie verwijderen vóór de upgrade
      + "pip uninstall azureml-train-automl"
      + "pip install azureml-train-automl"

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-runtime**
    + AutoML houdt nu rekening met klassen waar en onwaar bij het berekenen van de gemiddelde scalaire metrische gegevens voor binaire classificatietaken.
    + Machine learning- en trainingscode in AzureML-AutoML-Core verplaatst naar een nieuw pakket AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + Wanneer u aanroept op een gelabelde gegevensset met de downloadoptie, kunt u nu opgeven of u bestaande bestanden wilt `to_pandas_dataframe` overschrijven of niet.
    + Bij het aanroepen van of die resulteert in het verwijderen van een tijdreeks, label of afbeeldingskolom, worden ook de bijbehorende mogelijkheden voor de `keep_columns` `drop_columns` gegevensset uitgevallen.
    + Er is een probleem opgelost met het pytorch-laadprogramma voor de objectdetectietaak.
  + **azureml-contrib-interpret**
    + Uitlegdashboardwidget verwijderd uit azureml-contrib-interpret, pakket gewijzigd zodat het verwijst naar het nieuwe in interpret_community
    + Versie van interpret-community bijgewerkt naar 0.2.0
  + **azureml-core**
    + Verbeter de prestaties van `workspace.datasets` .
    + De mogelijkheid toegevoegd om gegevens te registreren Azure SQL Database met behulp van gebruikersnaam- en wachtwoordverificatie
    + Oplossing voor het laden van RunConfigurations vanuit relatieve paden.
    + Bij het aanroepen van of die resulteert in het verwijderen van een tijdreekskolom, worden ook de bijbehorende mogelijkheden voor `keep_columns` `drop_columns` de gegevensset uitgevallen.
  + **azureml-interpret**
    + bijgewerkte versie van interpret-community naar 0.2.0
  + **azureml-pipeline-steps**
    + Gedocumenteerde ondersteunde waarden `runconfig_pipeline_params` voor voor Azure machine learning pijplijnstappen.
  + **azureml-pipeline-core**
    + CLI-optie toegevoegd om uitvoer te downloaden in json-indeling voor pijplijnopdrachten.
  + **azureml-train-automl**
    + Splits AzureML-Train-AutoML in twee pakketten: een clientpakket AzureML-Train-AutoML-Client en een ML-trainingspakket AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Er is een thin client toegevoegd voor het verzenden van AutoML-experimenten zonder dat u lokale machine learning hoeft te installeren.
    + Logboekregistratie van automatisch gedetecteerde vertragingen, de grootte van het rolling-venster en de maximale horizon in de externe runs is opgelost.
  + **azureml-train-automl-runtime**
    + Er is een nieuw AutoML-pakket toegevoegd om machine learning en runtimeonderdelen van de client te isoleren.
  + **azureml-contrib-train-rl**
    + Ondersteuning voor bekrachtiging van leren toegevoegd in de SDK.
    + AmlWindowsCompute-ondersteuning toegevoegd in RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK voor Python v1.0.74

  + **Preview-functies**
    + **azureml-contrib-dataset**
      + Nadat u azureml-contrib-dataset hebt geïmporteerd, kunt u aanroepen in plaats van `Dataset.Labeled.from_json_lines` `._Labeled` een gelabelde gegevensset te maken.
      + Wanneer u aanroept op een gelabelde gegevensset met de downloadoptie, kunt u nu opgeven of u bestaande bestanden wilt `to_pandas_dataframe` overschrijven of niet.
      + Bij het aanroepen van of die resulteert in het verwijderen van een tijdreeks, label of afbeeldingskolom, worden ook de bijbehorende mogelijkheden voor `keep_columns` `drop_columns` de gegevensset uitgevallen.
      + Problemen opgelost met PyTorch-loader bij het aanroepen `dataset.to_torchvision()` van .

+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + Modelprofilering toegevoegd aan de preview-CLI.
    + Oplossing voor wijziging die fouten veroorzaakt in Azure Storage waardoor AzureML CLI mislukt.
    + Type Load Balancer toegevoegd aan MLC voor AKS-typen
  + **azureml-automl-core**
    + Er is een probleem opgelost met de detectie van een maximale periode voor tijdreeksen, met ontbrekende waarden en meerdere grains.
    + Probleem opgelost met fouten tijdens het genereren van kruisvalidatiesplitsingen.
    + Vervang deze sectie door een bericht in markdown-indeling dat moet worden weergegeven in de releaseopmerkingen: - Verbeterde verwerking van korte grains in de gegevenssets voor prognoses.
    + Het probleem met het maskeren van bepaalde gebruikersgegevens tijdens de logboekregistratie is opgelost. -Verbeterde logboekregistratie van de fouten tijdens het voorspellen.
    + Psutil toevoegen als conda-afhankelijkheid aan het automatisch gegenereerde yml-implementatiebestand.
  + **azureml-contrib-platform**
    + Oplossing voor wijziging die fouten veroorzaakt in Azure Storage waardoor AzureML CLI mislukt.
  + **azureml-core**
    + Lost een fout op die ervoor heeft gezorgd dat modellen die zijn Azure Functions 500-fouten produceren.
    + Er is een probleem opgelost waarbij het amlignore-bestand niet werd toegepast op momentopnamen.
    + Er is een nieuwe API amlcompute.get_active_runs die een generator retourneert voor het uitvoeren van en in de wachtrij geplaatste runs op een bepaalde amlcompute.
    + Type Load Balancer toegevoegd aan MLC voor AKS-typen.
    + Er append_prefix parameter bool toegevoegd aan download_files in run.py en download_artifacts_from_prefix in artifacts_client. Deze vlag wordt gebruikt om het oorspronkelijke bestandspad selectief plat te maken, zodat alleen de bestands- of mapnaam wordt toegevoegd aan de output_directory
    + Deserialisatieprobleem opgelost voor met `run_config.yml` het gebruik van gegevenssets.
    + Bij het aanroepen van of die resulteert in het verwijderen van een tijdreekskolom, worden ook de bijbehorende mogelijkheden voor `keep_columns` `drop_columns` de gegevensset weggetrokken.
  + **azureml-interpret**
    + Interpret-community-versie bijgewerkt naar 0.1.0.3
  + **azureml-train-automl**
    + Er is een probleem opgelost waarbij automl_step validatieproblemen mogelijk niet afdrukde.
    + Probleem register_model opgelost, zelfs als er lokaal afhankelijkheden ontbreken in de omgeving van het model.
    + Er is een probleem opgelost waarbij sommige externe runs niet docker waren ingeschakeld.
    + Voeg logboekregistratie toe van de uitzondering waardoor een lokale run voortijdig mislukt.
  + **azureml-train-core**
    + Overweeg resume_from worden uitgevoerd in de berekening van automatische afstemming van hyperparameters op de beste onderliggende runs.
  + **azureml-pipeline-core**
    + Probleem opgelost met het verwerken van parameters in het bouwen van pijplijnargumenten.
    + Beschrijving van pijplijn toegevoegd en yaml-parameter voor staptype toegevoegd.
    + Nieuwe yaml-indeling voor pijplijnstap en waarschuwing voor afschaffing toegevoegd voor oude indeling.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webervaring

De landingspagina van de [https://ml.azure.com](https://ml.azure.com) samenwerkingswerkruimte op is uitgebreid en hernoemd als de Azure Machine Learning-studio.

Vanuit de studio kunt u uw assets, zoals gegevenssets, pijplijnen, modellen, eindpunten, trainen, implementeren en beheren Azure Machine Learning gegevenssets, pijplijnen, modellen, eindpunten en meer.

Krijg toegang tot de volgende webgebaseerde ontwerphulpprogramma's vanuit de studio:

| Webhulpprogramma | Description | 
|-|-|-|
| Notebook-VM (preview) | Volledig beheerd cloudwerkstation | 
| [Automatische machine learning](tutorial-first-experiment-automated-ml.md) (preview) | Geen code-ervaring voor het automatiseren van machine learning modelontwikkeling | 
| [Designer](concept-designer.md) | Slepen en neerzetten machine learning voorheen bekend als de visuele interface | 


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning designer-verbeteringen

+ Voorheen bekend als de visuele interface 
+    11 nieuwe [modules,](algorithm-module-reference/module-reference.md) waaronder aanbevelingen, classificaties en trainingsprogramma's, waaronder feature engineering, kruisvalidatie en gegevenstransformatie.

### <a name="r-sdk"></a>R SDK 
 
Gegevenswetenschappers en AI-ontwikkelaars gebruiken [de Azure Machine Learning SDK](https://github.com/Azure/azureml-sdk-for-r) voor R om machine learning-werkstromen te bouwen en uit te voeren met Azure Machine Learning.

De Azure Machine Learning SDK voor R gebruikt het `reticulate` pakket om te verbinden met de Python SDK. Door rechtstreeks met Python te binden, kunt u met de SDK voor R toegang krijgen tot kernobjecten en -methoden die in de Python-SDK zijn geïmplementeerd vanuit elke R-omgeving die u kiest.

De belangrijkste mogelijkheden van de SDK zijn:

+    Cloudresources voor het bewaken, loggen en organiseren van uw machine-leerexperimenten beheren.
+    Modellen trainen met behulp van cloudbronnen, waaronder gpu-versnelde modeltraining.
+    Implementeer uw modellen als webservices op Azure Container Instances (ACI) en Azure Kubernetes Service (AKS).

Zie de [pakketwebsite voor](https://azure.github.io/azureml-sdk-for-r) volledige documentatie.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integratie met Event Grid 

Azure Machine Learning nu een resourceprovider voor Event Grid is, kunt u uw machine learning via de Azure Portal of Azure CLI. Gebruikers kunnen gebeurtenissen maken voor uitvoering, modelregistratie, modelimplementatie en gedetecteerde gegevensdrift. Deze gebeurtenissen kunnen worden gerouteerd naar gebeurtenis-handlers die worden ondersteund door Event Grid voor gebruik. Zie machine learning [gebeurtenisschema en](../event-grid/event-schema-machine-learning.md) [zelfstudieartikelen](how-to-use-event-grid.md) voor meer informatie.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK voor Python v1.0.72

+ **Nieuwe functies**
  + Er zijn gegevenssetmonitors toegevoegd via het [**azureml-datadrift-pakket,**](/python/api/azureml-datadrift) zodat tijdreeksgegevenssets kunnen worden gecontroleerd op gegevensdrift of andere statistische wijzigingen in de tijd. Waarschuwingen en gebeurtenissen kunnen worden geactiveerd als er drift wordt gedetecteerd of als aan andere voorwaarden voor de gegevens wordt voldaan. Zie [onze documentatie](how-to-monitor-datasets.md) voor meer informatie.
  + Aankondiging van twee nieuwe edities (ook wel door elkaar aangeduid als een SKU) in Azure Machine Learning. Met deze release kunt u nu een Basic- of Enterprise Azure Machine Learning maken. Alle bestaande werkruimten worden standaard ingesteld op de Basic-editie en u kunt op elk gewenst moment naar de Azure Portal of naar de studio gaan om de werkruimte bij te werken. U kunt een Basic- of Enterprise-werkruimte maken op basis van Azure Portal. Lees [onze documentatie](./how-to-manage-workspace.md) voor meer informatie. Vanuit de SDK kan de editie van uw werkruimte worden bepaald met behulp van de eigenschap 'sku' van uw werkruimteobject.
  + We hebben ook verbeteringen aangebracht in Azure Machine Learning Compute: u kunt nu metrische gegevens voor uw clusters (zoals het totale aantal knooppunten, het uitvoeren van knooppunten, het totale kernquotum) in Azure Monitor bekijken, naast het weergeven van diagnostische logboeken voor foutopsporing. Daarnaast kunt u ook de momenteel lopende of in de wachtrij geplaatste runs op uw cluster bekijken, evenals details zoals de IPs van de verschillende knooppunten in uw cluster. U kunt deze weergeven in de portal of met behulp van bijbehorende functies in de SDK of CLI.

  + **Preview-functies**
    + Er wordt preview-ondersteuning voor schijfversleuteling van uw lokale SSD in Azure Machine Learning Compute. Maak een technische ondersteuningsticket aan om uw abonnement te laten zien dat deze functie mag worden gebruikt.
    + Openbare preview van Azure Machine Learning Batch-deference. Azure Machine Learning batchdeferentie is gericht op grote deferentietaken die niet tijdgevoelig zijn. Batchdeferentie biedt rendabele deference compute-schaalbaarheid, met ongeëvenaarde doorvoer voor asynchrone toepassingen. Het is geoptimaliseerd voor hoge doorvoer, brand- en vergeetdeferentie over grote verzamelingen gegevens.
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
        + Ingeschakelde functies voor gelabelde gegevensset
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

+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + CLI biedt nu ondersteuning voor het verpakken van modellen.
    + CLI voor gegevensset toegevoegd. Voor meer informatie: `az ml dataset --help`
    + Ondersteuning toegevoegd voor het implementeren en verpakken van ondersteunde modellen (ONNX, scikit-learn en TensorFlow) zonder een InferenceConfig-exemplaar.
    + Overschrijfvlag toegevoegd voor service-implementatie (ACI en AKS) in SDK en CLI. Indien opgegeven, wordt de bestaande service overschreven als de service met de naam al bestaat. Als de service niet bestaat, maakt nieuwe service.
    + Modellen kunnen worden geregistreerd met twee nieuwe frameworks: Onnx en Tensorflow. - Modelregistratie accepteert voorbeeldinvoergegevens, voorbeelduitvoergegevens en resourceconfiguratie voor het model.
  + **azureml-automl-core**
    + Het trainen van een iteratie wordt alleen in een onderliggend proces uitgevoerd wanneer runtimebeperkingen worden ingesteld.
    + Er is een beveiliging toegevoegd voor het voorspellen van taken, om te controleren of een opgegeven max_horizon een geheugenprobleem op de opgegeven computer veroorzaakt of niet. Als dat zo is, wordt er een beveiligingsbericht weergegeven.
    + Ondersteuning toegevoegd voor complexe frequenties, zoals twee jaar en één maand. -Er is een begrijpelijk foutbericht toegevoegd als de frequentie niet kan worden bepaald.
    + Azureml-defaults toevoegen aan automatisch gegenereerde conda env om de implementatiefout van het model op te lossen
    + Toestaan dat tussenliggende gegevens in Azure Machine Learning pijplijn worden geconverteerd naar een tabellaire gegevensset en worden gebruikt in `AutoMLStep` .
    + Update voor kolomdoeleinden geïmplementeerd voor streaming.
    + Update van transformerparameters geïmplementeerd voor Impache en HashOneHotEncoder voor streaming.
    + De huidige gegevensgrootte en de minimaal vereiste gegevensgrootte zijn toegevoegd aan de validatiefoutberichten.
    + De minimaal vereiste gegevensgrootte voor kruisvalidatie is bijgewerkt om te garanderen dat er minimaal twee steekproeven per validatie worden gevouwen.
  + **azureml-cli-common**
    + CLI ondersteunt nu modelverpakking.
    + Modellen kunnen worden geregistreerd bij twee nieuwe frameworks: Onnx en Tensorflow.
    + Modelregistratie accepteert voorbeeldinvoergegevens, voorbeelduitvoergegevens en resourceconfiguratie voor het model.
  + **azureml-contrib-gbdt**
    + probleem opgelost met het releasekanaal voor de notebook
    + Er is een waarschuwing toegevoegd voor een niet-AmlCompute-rekendoel dat niet wordt ondersteund
    + LightGMB Estimator toegevoegd aan azureml-contrib-gbdt-pakket
  + [**azureml-core**](/python/api/azureml-core)
    + CLI ondersteunt nu modelverpakking.
    + Waarschuwing voor afschaffing toegevoegd voor afgeschafte gegevensset-API's. Zie Wijzigingsbericht gegevensset-API op https://aka.ms/tabular-dataset .
    + Wijzig [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) om de registratienaam en -versie te retourneren als de gegevensset is geregistreerd.
    + Er is een fout opgelost die ScriptRunConfig met de gegevensset als argument niet herhaaldelijk kan gebruiken om een experimentuit te voeren.
    + Gegevenssets die tijdens een run worden opgehaald, worden bijgespoord en kunnen worden weergegeven op de pagina met details van de run of door aan te roepen [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) nadat de run is voltooid.
    + Sta toe dat tussenliggende gegevens in Azure Machine Learning Pijplijn worden geconverteerd naar een tabellaire gegevensset en worden gebruikt in [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) .
    + Ondersteuning toegevoegd voor het implementeren en verpakken van ondersteunde modellen (ONNX, scikit-learn en TensorFlow) zonder een InferenceConfig-exemplaar.
    + Vlag voor overschrijven toegevoegd voor service-implementatie (ACI en AKS) in SDK en CLI. Indien opgegeven, wordt de bestaande service overschreven als de service met de naam al bestaat. Als de service niet bestaat, maakt nieuwe service.
    +  Modellen kunnen worden geregistreerd met twee nieuwe frameworks: Onnx en Tensorflow. Modelregistratie accepteert voorbeeldinvoergegevens, voorbeelduitvoergegevens en resourceconfiguratie voor het model.
    + Nieuwe gegevensstore toegevoegd voor Azure Database for MySQL. Voorbeeld toegevoegd voor het gebruik Azure Database for MySQL in DataTransferStep in Azure Machine Learning Pipelines.
    + Functionaliteit toegevoegd om tags toe te voegen aan en te verwijderen uit experimenten Functionaliteit toegevoegd om tags uit runs te verwijderen
    + Overschrijfvlag toegevoegd voor service-implementatie (ACI en AKS) in SDK en CLI. Indien opgegeven, wordt de bestaande service overschreven als de service met de naam al bestaat. Als de service niet bestaat, maakt nieuwe service.
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + Verplaatst van `azureml-contrib-datadrift` naar `azureml-datadrift`
    + Ondersteuning toegevoegd voor het bewaken van tijdreeks-gegevenssets voor drift en andere statistische metingen
    + Nieuwe methoden `create_from_model()` en in de klasse `create_from_dataset()` [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) . De `create()` methode wordt afgeschaft.
    + Aanpassingen aan de visualisaties in Python en de gebruikersinterface in Azure Machine Learning-studio.
    + Ondersteuning voor wekelijkse en maandelijkse controleplanning, naast dagelijkse voor gegevenssetmonitors.
    + Ondersteuning voor het invullen van metrische gegevens van gegevensmonitors voor het analyseren van historische gegevens voor gegevenssetmonitors.
    + Verschillende oplossingen voor fouten
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + azureml-dataprep is niet langer nodig voor het verzenden van een Azure Machine Learning Pipeline-run vanuit het `yaml` pijplijnbestand.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Voeg azureml-defaults toe aan automatisch gegenereerde conda env om de fout in de modelimplementatie op te lossen
    + De externe AutoML-training bevat nu azureml-defaults om hergebruik van trainings-env voor de deferentie mogelijk te maken.
  + **azureml-train-core**
    + PyTorch 1.3-ondersteuning toegevoegd in [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch) estimator

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Visuele interface (preview)

+ De Azure Machine Learning visuele interface (preview) is vernieuwd om te worden uitgevoerd [op Azure Machine Learning pijplijnen.](concept-ml-pipelines.md) Pijplijnen (voorheen experimenten genoemd) die in de visuele interface zijn gemaakt, zijn nu volledig geïntegreerd met de Azure Machine Learning ervaring.
  + Uniforme beheerervaring met SDK-assets
  + Versiering en tracering voor visuele interfacemodellen, pijplijnen en eindpunten
  + Opnieuw ontworpen gebruikersinterface
  + Implementatie van batchdeferentie toegevoegd
  + Ondersteuning Azure Kubernetes Service (AKS) toegevoegd voor de deferentierekendoelen
  + Nieuwe werkstroom voor het schrijven van pijplijnen in python-stappen
  + Nieuwe [landingspagina](https://ml.azure.com) voor visuele ontwerphulpprogramma's

+ **Nieuwe modules**
  + Wiskundige bewerking toepassen
  + SQL-transformatie toepassen
  + Waarden vastborden
  + Gegevens samenvatten
  + Importeren vanuit SQL Database

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK voor Python v1.0.69

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + Modelverklaringen beperken tot beste uitvoering in plaats van uitleg bij elke uitvoering. Dit gedrag wordt gewijzigd voor lokaal, extern en ADB.
    + Ondersteuning toegevoegd voor uitleg over on-demand modellen voor gebruikersinterface
    + Psutil toegevoegd als afhankelijkheid van `automl` en opgenomen psutil als een conda-afhankelijkheid in amlcompute.
    + Het probleem met heuristische vertragingen en de grootte van het doorrollen van vensters in de gegevenssets met prognoses is opgelost. Een reeks die lineaire algebrafouten kan veroorzaken
      + Er is een afdruk toegevoegd voor de heuristisch bepaalde parameters in de prognoseuit runs.
  + **azureml-contrib-datadrift**
    + Beveiliging toegevoegd tijdens het maken van metrische uitvoergegevens als afwijking op gegevenssetniveau zich niet in de eerste sectie voordeed.
  + **azureml-contrib-interpret**
    + azureml-contrib-explain-model package is hernoemd naar azureml-contrib-interpret
  + **azureml-core**
    + API toegevoegd om de registratie van gegevenssets ongedaan te maken. `dataset.unregister_all_versions()`
    + azureml-contrib-explain-model package is hernoemd naar azureml-contrib-interpret.
  + **[azureml-core](/python/api/azureml-core)**
    + API toegevoegd om de registratie van gegevenssets ongedaan te maken. Dataset. [unregister_all_versions()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Gegevensset-API toegevoegd om de gewijzigde tijd van gegevens te controleren. `dataset.data_changed_time`.
    + En kunnen gebruiken `FileDataset` als invoer voor , en in `TabularDataset` `PythonScriptStep` `EstimatorStep` `HyperDriveStep` Azure Machine Learning-pijplijn
    + De prestaties `FileDataset.mount` van zijn verbeterd voor mappen met een groot aantal bestanden
    + [FileDataset](/python/api/azureml-core/azureml.data.filedataset) en [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) kunnen worden gebruikt als invoer voor [PythonScriptStep,](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)en [HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) in de Azure Machine Learning-pijplijn.
    + Prestaties van FileDataset. [mount()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) is verbeterd voor mappen met een groot aantal bestanden
    + URL toegevoegd aan bekende foutaanbevelingen in details van de run.
    + Er is een fout opgelost in run.get_metrics waar aanvragen zouden mislukken als een run te veel kinderen had
    + Er is een fout opgelost in [run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) waar aanvragen zouden mislukken als een run te veel kinderen had
    + Ondersteuning toegevoegd voor verificatie in Arcadia-cluster.
    + Als u een Experiment-object maakt, wordt het experiment in de werkruimte Azure Machine Learning voor het bijhouden van de run history. De experiment-id en de gearchiveerde tijd worden ingevuld in het Experiment-object bij het maken. Voorbeeld: experiment = Experiment(workspace, "New Experiment") experiment_id = experiment.id archive() en reactivate() zijn functies die kunnen worden aangeroepen voor een experiment om het experiment te verbergen en te herstellen, zodat het niet wordt weergegeven in de UX of standaard wordt geretourneerd in een aanroep van experimenten. Als er een nieuw experiment wordt gemaakt met dezelfde naam als een gearchiveerd experiment, kunt u de naam van het gearchiveerde experiment wijzigen wanneer het opnieuw wordt geactiveerd door een nieuwe naam door te geven. Er kan slechts één actief experiment met een bepaalde naam zijn. Voorbeeld: experiment1 = Experiment(workspace, "Active Experiment") experiment1.archive() # Maak een nieuw actief experiment met dezelfde naam als het gearchiveerde experiment. experiment2. = Experiment(workspace, "Active Experiment") experiment1.reactivate(new_name="Previous Active Experiment") De statische methode list() op Experiment kan een naamfilter en ViewType-filter nemen. ViewType-waarden zijn 'ACTIVE_ONLY', 'ARCHIVED_ONLY' en 'ALL' Voorbeeld: archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + Ondersteuning voor het gebruik van de omgeving voor modelimplementatie en service-update
  + **azureml-datadrift**
    + Het kenmerk show van de klasse DataDriftDector biedt geen ondersteuning meer voor het optionele argument 'with_details'. Het kenmerk show presenteert alleen de coëfficiënt voor gegevensdrift en de bijdrage van gegevensdrift van functiekolommen.
    + Gedrag van het kenmerk DataDriftDetector 'get_output' gewijzigd:
      + Invoerparameters start_time, end_time zijn optioneel in plaats van verplicht;
      + Invoerspecifieke start_time en/of end_time met een specifieke run_id in dezelfde aanroep resulteren in een uitzondering voor waardefouten omdat ze elkaar uitsluiten
      + Door specifieke start_time en/of end_time, worden alleen resultaten van geplande runs geretourneerd;
      + Parameter 'daily_latest_only' is afgeschaft.
    + Ondersteuning voor het ophalen van gegevenssetgebaseerde gegevensdriftuitvoer.
  + **azureml-explain-model**
    + Wijzigt de naam van het AzureML-explain-model-pakket in AzureML-interpret, waarbij het oude pakket voor nu achterwaartse compatibiliteit wordt behouden
    + bug `automl` opgelost met onbewerkte uitleg ingesteld op classificatietaak in plaats van regressie standaard bij downloaden van ExplanationClient
    + Ondersteuning voor toevoegen `ScoringExplainer` om rechtstreeks te worden gemaakt met `MimicWrapper`
  + **azureml-pipeline-core**
    + Verbeterde prestaties bij het maken van een grote pijplijn
  + **azureml-train-core**
    + TensorFlow 2.0-ondersteuning toegevoegd in TensorFlow Estimator
  + **azureml-train-automl**
    + Als u een [Experiment-object](/python/api/azureml-core/azureml.core.experiment.experiment) maakt, wordt het experiment in de werkruimte Azure Machine Learning voor het bijhouden van de run history. De experiment-id en gearchiveerde tijd worden ingevuld in het experimentobject bij het maken. Voorbeeld:

        ```python
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) en [reactivate()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) zijn functies die kunnen worden aangeroepen voor een experiment om het experiment te verbergen en te herstellen, zodat het niet wordt weergegeven in de UX of standaard wordt geretourneerd in een aanroep van een lijst met experimenten. Als er een nieuw experiment wordt gemaakt met dezelfde naam als een gearchiveerd experiment, kunt u de naam van het gearchiveerde experiment wijzigen wanneer het opnieuw wordt geactiveerd door een nieuwe naam door te geven. Er kan slechts één actief experiment zijn met een bepaalde naam. Voorbeeld:

        ```python
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        De statische [methode list()](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) op Experiment kan een naamfilter en ViewType-filter gebruiken. ViewType-waarden zijn 'ACTIVE_ONLY', 'ARCHIVED_ONLY' en 'ALL'. Voorbeeld:

        ```python
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Ondersteuning voor het gebruik van omgeving voor modelimplementatie en service-update.
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + Het kenmerk show van [de klasse DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) biedt geen ondersteuning meer voor het optionele argument 'with_details'. Het kenmerk show presenteert alleen de coëfficiënt voor gegevensdrift en de bijdrage van gegevensdrift van functiekolommen.
    + De functie DataDriftDetector [get_output]python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) gedragswijzigingen:
      + Invoerparameter start_time, end_time zijn optioneel in plaats van verplicht;
      + Invoerspecifieke start_time en/of end_time met een specifieke run_id in dezelfde aanroep leiden tot een uitzondering van waardefouten omdat ze elkaar uitsluiten;
      + Door specifieke invoer start_time en/of end_time, worden alleen resultaten van geplande runs geretourneerd;
      + Parameter 'daily_latest_only' is afgeschaft.
    + Ondersteuning voor het ophalen van gegevensdriftuitvoer op basis van gegevenssets.
  + **azureml-explain-model**
    + Ondersteuning voor [ScoringExplainer toevoegen om](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer) rechtstreeks te worden gemaakt met MimicWrapper
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Verbeterde prestaties bij het maken van een grote pijplijn.
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + TensorFlow 2.0-ondersteuning toegevoegd in [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + De bovenliggende run wordt niet meer uitgevoerd wanneer de installatie-iteratie is mislukt, omdat de orchestration dit al doet.
    + Ondersteuning voor local-docker en local-conda toegevoegd voor AutoML-experimenten
    + Ondersteuning voor local-docker en local-conda toegevoegd voor AutoML-experimenten.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nieuwe webervaring (preview) voor Azure Machine Learning werkruimten

Het tabblad Experiment in de [nieuwe werkruimteportal](https://ml.azure.com) is bijgewerkt, zodat gegevenswetenschappers experimenten op een efficiëntere manier kunnen bewaken. U kunt de volgende functies verkennen:
+ Metagegevens van experimenten om eenvoudig uw lijst met experimenten te filteren en sorteren
+ Vereenvoudigde en goed presterende experimentdetailpagina's waarmee u uw uitvoeringen kunt visualiseren en vergelijken
+ Nieuw ontwerp voor het uitvoeren van detailpagina's om uw trainingsruns te begrijpen en te bewaken

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK voor Python v1.0.65

  + **Nieuwe functies**
    + Gecureerde omgevingen toegevoegd. Deze omgevingen zijn vooraf geconfigureerd met bibliotheken voor algemene machine learning-taken en zijn vooraf gebouwd en in de cache opgeslagen als Docker-afbeeldingen voor snellere uitvoering. Ze worden standaard weergegeven in de lijst met omgevingen van de werkruimte, met het voorvoegsel 'AzureML'.
    + Gecureerde omgevingen toegevoegd. Deze omgevingen zijn vooraf geconfigureerd met bibliotheken voor algemene machine learning-taken en zijn vooraf gebouwd en in de cache opgeslagen als Docker-afbeeldingen voor snellere uitvoering. Ze worden standaard weergegeven in [de](/python/api/azureml-core/azureml.core.workspace%28class%29)lijst met omgevingen van de werkruimte, met het voorvoegsel 'AzureML'.

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Ondersteuning voor ONNX-conversie toegevoegd voor ADB en HDI

+ **Preview-functies**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Ondersteunde SMS en BiLSTM als tekst-featurizer (alleen preview)
    + Ondersteunde featurization-aanpassing voor kolom- en transformatorparameters (alleen preview)
    + Ondersteunde onbewerkte uitleg wanneer de gebruiker model uitleg tijdens de training in staat stelt (alleen preview)
    + Er is Een voor `timeseries` prognose toegevoegd als een trainbare pijplijn (alleen preview)

  + **azureml-contrib-datadrift**
    + Pakketten verplaatst van azureml-contrib-datadrift naar azureml-datadrift; het `contrib` pakket wordt verwijderd in een toekomstige release

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + FeaturizationConfig geïntroduceerd in AutoMLConfig en AutoMLBaseSettings
    + FeaturizationConfig geïntroduceerd in [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) en AutoMLBaseSettings
      + Kolomdoel voor featurisatie overschrijven met opgegeven kolom en functietype
      + Transformerparameters overschrijven
    + Afschaffingsbericht toegevoegd voor explain_model() en retrieve_model_explanations()
    + Added Added Added as a trainable pipeline (preview only)
    + Afschaffingsbericht toegevoegd voor explain_model() en retrieve_model_explanations().
    + Added Added Added as a trainable pipeline (preview only).
    + Er is ondersteuning toegevoegd voor automatische detectie van doelvertraging, de grootte van het doorrollende venster en de maximale horizon. Als een van target_lags, target_rolling_window_size of max_horizon is ingesteld op 'auto', wordt de heuristiek toegepast om de waarde van de bijbehorende parameter te schatten op basis van trainingsgegevens.
    + Er is een probleem opgelost met prognoses in het geval dat een gegevensset één grain-kolom bevat, dit grain van een numeriek type is en er een hiaat is tussen trainen en testset
    + Foutbericht over de gedupliceerde index in de externe run in prognosetaken opgelost
    + Er is een probleem opgelost met prognoses in het geval dat een gegevensset één grain-kolom bevat, dit grain van een numeriek type is en er een hiaat is tussen trainen en testset.
    + Het foutbericht over de gedupliceerde index in de externe run in prognosetaken is opgelost.
    + Er is een beveiliging toegevoegd om te controleren of een gegevensset niet in balans is of niet. Als dat zo is, wordt er een beveiligingsbericht naar de console geschreven.
  + **azureml-core**
    + Mogelijkheid toegevoegd om sas-URL op te halen voor model in opslag via het modelobject. Bijvoorbeeld: model.get_sas_url()
    + Introductie `run.get_details()['datasets']` om gegevenssets op te halen die zijn gekoppeld aan de verzonden run
    + Voeg API toe `Dataset.Tabular.from_json_lines_files` om een TabularDataset te maken op basis van JSON Lines-bestanden. Ga naar dit artikel voor documentatie voor meer informatie over [](how-to-create-register-datasets.md) deze tabellaire gegevens in JSON Lines-bestanden in TabularDataset.
    + Aanvullende velden voor de VM-grootte (besturingssysteemschijf, aantal GPU's) toegevoegd aan de functie supported_vmsizes ()
    + Extra velden toegevoegd aan de functie list_nodes () om de run, het privé- en openbare IP-adres, de poort, enzovoort weer te geven.
    + Mogelijkheid om een nieuw veld op te geven tijdens het inrichten van het cluster --remotelogin_port_public_access dat kan worden ingesteld op in- of uitgeschakeld, afhankelijk van of u de SSH-poort open wilt laten of gesloten wilt laten op het moment dat het cluster wordt gemaakt. Als u deze niet opgeeft, wordt de poort slim geopend of gesloten, afhankelijk van of u het cluster in een VNet implementeert.
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + Mogelijkheid toegevoegd om sas-URL op te halen voor model in opslag via het modelobject. Bijvoorbeeld: model. [get_sas_url()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Voer de introductie uit. [get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] om gegevenssets op te halen die zijn gekoppeld aan de verzonden run
    + Voeg API `Dataset.Tabular` toe.[ from_json_lines_files() om](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) een TabularDataset te maken op basis van JSON Lines-bestanden. Ga naar voor documentatie voor meer informatie over deze tabellaire gegevens in JSON Lines-bestanden in TabularDataset. https://aka.ms/azureml-data
    + Aanvullende velden voor de VM-grootte (besturingssysteemschijf, aantal GPU's) toegevoegd aan [de functie supported_vmsizes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Extra velden toegevoegd aan de [functie list_nodes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) om de run, het privé- en het openbare IP-adres, de poort, enzovoort weer te geven.
    + Mogelijkheid om een nieuw veld op te geven tijdens het inrichten van het [cluster](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)  dat kan worden ingesteld op in- of uitgeschakeld, afhankelijk van of u de SSH-poort open of gesloten wilt laten op het moment dat het cluster wordt gemaakt. Als u deze niet opgeeft, wordt de poort slim geopend of gesloten, afhankelijk van of u het cluster in een VNet implementeert.
  + **azureml-explain-model**
    + Verbeterde documentatie voor Uitleguitvoer in het classificatiescenario.
    + De mogelijkheid toegevoegd om de voorspelde y-waarden te uploaden in de uitleg voor de evaluatievoorbeelden. Ontgrendelt meer nuttige visualisaties.
    + De eigenschap explainer is toegevoegd aan MimicWrapper om het onderliggende MimicExplainer op te kunnen doen.
  + **azureml-pipeline-core**
    + Notebook toegevoegd om Module, ModuleVersion en ModuleStep te beschrijven
  + **azureml-pipeline-steps**
    + RScriptStep toegevoegd ter ondersteuning van het uitvoeren van R-scripts via een AML-pijplijn.
    + Probleem opgelost met parseren van metagegevensparameters in AzureBatchStep waardoor het foutbericht 'toewijzing voor parameter SubscriptionId is niet opgegeven' werd veroorzaakt.
  + **azureml-train-automl**
    + Ondersteunde training_data, validation_data, label_column_name, weight_column_name als gegevensinvoerindeling
    + Afschaffingsbericht toegevoegd voor explain_model() en retrieve_model_explanations()
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Er is [een notebook toegevoegd](https://aka.ms/pl-modulestep) om [Module](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29), [ModuleVersion en [ModuleStep te beschrijven.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + [RScriptStep toegevoegd ter ondersteuning](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) van het uitvoeren van R-scripts via AML-pijplijn.
    + Het parseren van metagegevensparameters in [AzureBatchStep waardoor het foutbericht 'toewijzing voor parameter SubscriptionId is niet opgegeven' werd veroorzaakt, is opgelost.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Ondersteunde training_data, validation_data, label_column_name, weight_column_name als gegevensinvoerindeling.
    + Afschaffingsbericht toegevoegd voor [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) [en retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK voor Python v1.0.62

+ **Nieuwe functies**
  + De eigenschap `timeseries`  tabularDataset is geïntroduceerd. Met deze eigenschap kunt u eenvoudig tijdstempels filteren op gegevens in TabularDataset, zoals het nemen van alle gegevens tussen een bepaalde tijd of de meest recente gegevens.  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb voor een voorbeeldnotenote.
  + Ingeschakelde training met TabularDataset en FileDataset. 

  + **azureml-train-core**
      + En `Nccl` ondersteuning toegevoegd in `Gloo` PyTorch-estimator

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + De AutoML-instelling 'lag_length' en laggingTransformer zijn afgeschaft.
    + Juiste validatie van invoergegevens opgelost als deze zijn opgegeven in een gegevensstroomindeling
    + De fit_pipeline.py gewijzigd om de json van de grafiek te genereren en te uploaden naar artefacten.
    + De grafiek wordt weergegeven onder `userrun` met behulp van `Cytoscape` .
  + **azureml-core**
    + De afhandeling van uitzonderingen in ADB-code is opnieuw opgetreden en wijzigingen aangebracht volgens de nieuwe foutafhandeling
    + Automatische MSI-verificatie toegevoegd voor notebook-VM's.
    + Probleem opgelost waarbij beschadigde of lege modellen konden worden geüpload vanwege mislukte nieuwe pogingen.
    + De fout opgelost waarbij `DataReference` de naam verandert wanneer de modus wordt gewijzigd `DataReference` (bijvoorbeeld bij het aanroepen `as_upload` van , of `as_download` `as_mount` ).
    + Maak `mount_point` en `target_path` optioneel voor en `FileDataset.mount` `FileDataset.download` .
    + Uitzondering dat de tijdstempelkolom niet kan worden gevonden, wordt afgekeurd als de tijdgebonden API wordt aangeroepen zonder dat er een goede tijdstempelkolom is toegewezen of als de toegewezen tijdstempelkolommen worden weggevallen.
    + Tijdseriekolommen moeten worden toegewezen aan een kolom waarvan het type Datum is, anders wordt er een uitzondering verwacht
    + Tijdsseriekolommen die API 'with_timestamp_columns' toewijzen, kunnen de kolomnaam Geen-waarde fine/coarse-tijdstempel nemen, waardoor eerder toegewezen tijdstempelkolommen worden gewist.
    + Er wordt een uitzondering weergegeven wanneer de kolom met een coarse-nerf of een fijnmainig tijdstempel wordt wegvallen met een indicatie voor de gebruiker dat verwijderen kan worden uitgevoerd nadat een kolom met tijdstempel in de lijst voor verwijderen is uitgevallen of als with_time_stamp wordt aanroepen met de waarde Geen om tijdstempelkolommen vrij te geven
    + Er wordt een uitzondering weergegeven wanneer een coarse-nerf of fijnmainige tijdstempelkolom niet is opgenomen in de lijst kolommen behouden, met een indicatie voor de gebruiker dat het bewaren kan worden uitgevoerd na het invoegen van een tijdstempelkolom in de lijst met keep-kolommen of het aanroepen van with_time_stamp met de waarde Geen om tijdstempelkolommen vrij te geven.
    + Logboekregistratie toegevoegd voor de grootte van een geregistreerd model.
  + **azureml-explain-model**
    + Waarschuwing opgelost die naar de console werd afgedrukt wanneer het Python-pakket 'inpakken' niet is geïnstalleerd: 'Met oudere dan ondersteunde versie van lightgbm kunt u een upgrade uitvoeren naar versie hoger dan 2.2.1'
    + Probleem opgelost met uitleg over downloadmodel met sharding voor globale uitleg met veel functies
    + Probleem opgelost met ontbrekende initialisatievoorbeelden voor explainer voor nabootsen in uitvoeruitvoer
    + Onveranderbare fout opgelost in seteigenschappen bij het uploaden met uitlegclient met behulp van twee verschillende typen modellen
    + Er is een get_raw toegevoegd aan scoring explainer.explain(), zodat één scoring-uitlegder zowel engineered als onbewerkte waarden kan retourneren.
  + **azureml-train-automl**
    + Openbare API's van AutoML geïntroduceerd voor ondersteuning van uitleg over SDK : nieuwere manier om AutoML-uitleg te ondersteunen door AutoML-featurization los te koppelen en SDK uit te leggen: geïntegreerde ondersteuning voor onbewerkte uitleg van `automl` azureml explain SDK voor AutoML-modellen.
    + Azureml-defaults verwijderen uit externe trainingsomgevingen.
    + Standaardlocatie van cacheopslag gewijzigd van FileCacheStore op basis van een naar AzureFileCacheStore één voor AutoML op Azure Databricks codepad.
    + Juiste validatie van invoergegevens opgelost als deze zijn opgegeven in een gegevensstroomindeling
  + **azureml-train-core**
    + Heeft de source_directory_data_store teruggedraaid.
    + Mogelijkheid toegevoegd om geïnstalleerde azureml-pakketversies te overschrijven.
    + Ondersteuning voor dockerfile toegevoegd aan `environment_definition` parameter in estimators.
    + Vereenvoudigde parameters voor gedistribueerde training in estimators.

         ```python
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nieuwe webervaring (preview) voor Azure Machine Learning werkruimten
Met de nieuwe webervaring kunnen gegevenswetenschappers en data engineers hun end-to-end machine learning-levenscyclus voltooien, van het voorbereiden en visualiseren van gegevens tot het trainen en implementeren van modellen op één locatie.

![Azure Machine Learning -werkruimte (preview)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Belangrijke functies:**

Met deze nieuwe Azure Machine Learning interface kunt u nu het volgende doen:
+ Uw notebooks beheren of een koppeling naar Jupyter maken
+ [Geautomatiseerde ML-experimenten uitvoeren](tutorial-first-experiment-automated-ml.md)
+ [Gegevenssets maken van lokale bestanden, gegevensstores & webbestanden](how-to-create-register-datasets.md)
+ Verkennen & gegevenssets voorbereiden voor het maken van modellen
+ Gegevensdrift voor uw modellen bewaken
+ Recente resources weergeven vanuit een dashboard

Op het moment van deze release worden de volgende browsers ondersteund: Chrome, Firefox, Safari en Microsoft Edge Preview.

**Bekende problemen:**

1. Vernieuw uw browser als u de fout 'Er is iets misgegaan! Fout bij het laden van segmentbestanden wanneer de implementatie wordt uitgevoerd.

1. Kan het bestand niet verwijderen of de naam wijzigen in Notebooks en Bestanden. Tijdens de openbare preview kunt u de Gebruikersinterface van Jupyter of Terminal in notebook-VM gebruiken om updatebestandsbewerkingen uit te voeren. Omdat het een aangesloten netwerkbestandssysteem is, worden alle wijzigingen die u aanmaakt op notebook-VM onmiddellijk weergegeven in de Notebook-werkruimte.

1. Als u SSH wilt gebruiken in de notebook-VM:
   1. Zoek de SSH-sleutels die zijn gemaakt tijdens de installatie van de VM. Of zoek de sleutels in de Azure Machine Learning-werkruimte > open het tabblad Compute > zoek notebook-VM in de lijst > de eigenschappen ervan openen: kopieer de sleutels uit het dialoogvenster.
   1. Importeer deze openbare en persoonlijke SSH-sleutels naar uw lokale computer.
   1. Gebruik deze om via SSH naar de notebook-VM te gaan.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK voor Python v1.0.60

+ **Nieuwe functies**
  + Hier is FileDataset geïntroduceerd, die verwijst naar een of meer bestanden in uw gegevensstores of openbare URL's. De bestanden kunnen elke indeling hebben. FileDataset biedt u de mogelijkheid om de bestanden te downloaden of aan uw rekenkracht te mounten. 
  + Yaml-ondersteuning voor pijplijn toegevoegd voor PythonScript Step, Adla Step, Databricks Step, DataTransferStep en AzureBatch Step

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + AutoArima is nu een pijplijn die alleen kan worden gebruikt als preview-versie.
    + Verbeterde foutrapportage voor prognoses.
    + De logboekregistratie verbeterd met behulp van aangepaste uitzonderingen in plaats van algemeen in de prognosetaken.
    + De controle op max_concurrent_iterations verwijderd om minder dan het totale aantal iteraties te zijn.
    + AutoML-modellen retourneren nu AutoMLExceptions
    + Deze release verbetert de uitvoeringsprestaties van geautomatiseerde machine learning lokale uitvoeringen.
  + **azureml-core**
    + Introduceer Dataset.get_all(workspace), die een woordenlijst met - en -objecten retourneert met `TabularDataset` `FileDataset` de registratienaam.

    ```python
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Introduceer `parition_format` als argument voor en `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files` . De partitiegegevens van elk gegevenspad worden geëxtraheerd in kolommen op basis van de opgegeven indeling. {column_name}' maakt een tekenreekskolom en '{column_name:yyyy/MM/dd/HH/mm/ss}' maakt een datum/tijd-kolom, waarbij 'yyyy', 'MM', 'dd', 'HH', 'mm' en 'ss' worden gebruikt om jaar, maand, dag, uur, minuut en seconde voor het datum/tijd-type te extraheren. De partition_format moet beginnen vanaf de positie van de eerste partitiesleutel tot het einde van het bestandspad. Bijvoorbeeld, op het pad '.. /USA/2019/01/01/data.csv' waarbij de partitie per land en tijd is, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' maakt de tekenreekskolom 'Country' met de waarde 'USA' en datum/tijdkolom 'PartitionDate' met de waarde '2019-01-01'.
        ```python
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Introduceer `partition_format` als argument voor en `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files` . De partitiegegevens van elk gegevenspad worden geëxtraheerd in kolommen op basis van de opgegeven indeling. {column_name}' maakt een tekenreekskolom en '{column_name:yyyy/MM/dd/HH/mm/ss}' maakt een datum/tijd-kolom, waarbij 'yyyy', 'MM', 'dd', 'HH', 'mm' en 'ss' worden gebruikt om jaar, maand, dag, uur, minuut en seconde voor het datum/tijd-type te extraheren. De partition_format moet beginnen vanaf de positie van de eerste partitiesleutel tot het einde van het bestandspad. Bijvoorbeeld, gegeven het pad '.. /USA/2019/01/01/data.csv' waarbij de partitie per land en tijd is, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' maakt de tekenreekskolom 'Country' met de waarde 'USA' en datum/tijdkolom 'PartitionDate' met de waarde '2019-01-01'.
    + `to_csv_files` - `to_parquet_files` en -methoden zijn toegevoegd aan `TabularDataset` . Deze methoden maken conversie tussen een en een mogelijk door de gegevens te converteren `TabularDataset` naar bestanden met de opgegeven `FileDataset` indeling.
    + Meld u automatisch aan bij het register met basisafbeeldingen wanneer u een Dockerfile opspart die is gegenereerd door Model.package().
    + 'gpu_support' is niet meer nodig; AML detecteert en gebruikt nu automatisch de nvidia docker-extensie wanneer deze beschikbaar is. Deze wordt verwijderd in een toekomstige release.
    + Ondersteuning toegevoegd voor het maken, bijwerken en gebruiken van PipelineDrafts.
    + Deze release verbetert de uitvoeringsprestaties van geautomatiseerde machine learning lokale uitvoeringen.
    + Gebruikers kunnen op naam metrische gegevens opvragen uit de rungeschiedenis.
    + De logboekregistratie is verbeterd met behulp van aangepaste uitzonderingen in plaats van algemeen in de prognosetaken.
  + **azureml-explain-model**
    + Er feature_maps parameter toegevoegd aan de nieuwe MimicWrapper, zodat gebruikers onbewerkte uitleg over de functie kunnen krijgen.
    + Uploads van gegevenssets zijn nu standaard uitgeschakeld voor uitleg over uploaden en kunnen opnieuw worden ingeschakeld met upload_datasets=True
    + Filterparameters 'is_law' toegevoegd aan de uitleglijst en downloadfuncties.
    + Methode toegevoegd `get_raw_explanation(feature_maps)` aan zowel globale als lokale uitlegobjecten.
    + Versiecontrole toegevoegd aan lightgbm met afgedrukte waarschuwing indien onderstaande ondersteunde versie
    + Geoptimaliseerd geheugengebruik bij batch-uitleg
    + AutoML-modellen retourneren nu AutoMLExceptions
  + **azureml-pipeline-core**
    + Ondersteuning toegevoegd voor het maken, bijwerken en gebruiken van PipelineDrafts: kan worden gebruikt om veranderlijke pijplijndefinities te onderhouden en interactief uit te voeren
  + **azureml-train-automl**
    + Er is een functie gemaakt voor het installeren van specifieke versies van pytorch v1.1.0 die geschikt zijn voor gpu, :::no-loc text="cuda"::: toolkit 9.0 en pytorch-which is vereist voor het inschakelen van WILT/XLNet in de externe python-runtime-omgeving.
  + **azureml-train-core**
    + Vroege fout van sommige hyperparameterruimtedefinitiefouten rechtstreeks in de SDK in plaats van serverzijde.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **Opgeloste fouten en verbeteringen**
  + Schrijven naar ADLS/ADLSGen2 is ingeschakeld met onbewerkte paden en referenties.
  + Er is een fout opgelost waardoor `include_path=True` niet werkte voor `read_parquet` .
  + Er is `to_pandas_dataframe()` een fout opgelost die werd veroorzaakt door uitzondering 'Ongeldige eigenschapswaarde: hostSecret'.
  + Er is een fout opgelost waarbij bestanden niet konden worden gelezen op DBFS in de Spark-modus.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK voor Python v1.0.57
+ **Nieuwe functies**
  + Ingeschakeld om `TabularDataset` te worden gebruikt door AutomatedML. Ga naar voor meer `TabularDataset` informatie over https://aka.ms/azureml/howto/createdatasets .

+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + U kunt nu het TLS/SSL-certificaat bijwerken voor het score-eindpunt dat op het AKS-cluster is geïmplementeerd, zowel voor het door Microsoft gegenereerde als het klantcertificaat.
  + **azureml-automl-core**
    + Er is een probleem opgelost in AutoML dat rijen met ontbrekende labels niet juist zijn verwijderd.
    + Verbeterde logboekregistratie van fouten in AutoML; volledige foutberichten worden nu altijd naar het logboekbestand geschreven.
    + AutoML heeft het vastmaken van pakketten bijgewerkt met `azureml-defaults` , `azureml-explain-model` en `azureml-dataprep` . AutoML geeft geen waarschuwing meer bij niet-overeenkomende pakketten (met uitzondering van `azureml-train-automl` pakket).
    + Er is een probleem opgelost waarbij `timeseries`  cv-splitsingen ongelijke grootte hebben, waardoor de berekening van de bin mislukt.
    + Als we bij het uitvoeren van ensemble-iteratie voor het trainingstype kruisvalidatie problemen hadden met het downloaden van de modellen die zijn getraind op de volledige gegevensset, hadden we een inconsistentie tussen de modelgewichten en de modellen die werden ingevoerd in het stemmende ensemble.
    + De fout die is opgetreden bij het trainen en/of valideren van labels (y en y_valid) is opgelost in de vorm van pandas-gegevensframe, maar niet als numpy-matrix.
    + Het probleem met de prognosetaken opgelost toen Geen werd aangetroffen in de Booleaanse kolommen van invoertabellen.
    + AutoML-gebruikers toestaan om trainingsreeksen te laten vallen die niet lang genoeg zijn bij het voorspellen. - AutoML-gebruikers toestaan om bij het voorspellen grains uit de testset te laten vallen die niet in de trainingsset aanwezig zijn.
  + **azureml-core**
    + Probleem opgelost met het blob_cache_timeout van parameters.
    + Externe uitzonderingstypen voor passend maken en transformeren toegevoegd aan systeemfouten.
    + Ondersteuning toegevoegd voor Key Vault voor externe runs. Voeg de klasse azureml.core.keyvault.Keyvault toe om geheimen toe te voegen, op te halen en weer te geven uit de sleutelkault die is gekoppeld aan uw werkruimte. Ondersteunde bewerkingen zijn:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(naam, waarde)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(naam)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Aanvullende methoden voor het verkrijgen van standaardsleutelkault en het verkrijgen van geheimen tijdens het uitvoeren op afstand:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(naam)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Aanvullende parameters voor overschrijven toegevoegd om de CLI-opdracht submit-hyperdrive te verzenden.
    + Door de betrouwbaarheid van API-aanroepen te verbeteren, worden nieuwe aanvragen uitgebreid naar bibliotheek-uitzonderingen voor veelvoorkomende aanvragen.
    + Voeg ondersteuning toe voor het indienen van runs vanaf een verzonden run.
    + Probleem met verlopen SAS-token opgelost in FileWatcher, waardoor bestanden niet meer werden geüpload nadat het eerste token was verlopen.
    + Ondersteuning voor het importeren van HTTP csv/tsv-bestanden in de python-SDK voor gegevenssets.
    + De methode Workspace.setup() is afgeschaft. Waarschuwingsbericht dat aan gebruikers wordt weergegeven, stelt voor om in plaats daarvan create() of get()/from_config() te gebruiken.
    + Er Environment.add_private_pip_wheel() toegevoegd, waarmee persoonlijke aangepaste Python-pakketten naar de werkruimte kunnen worden geüpload en veilig kunnen worden gebruikt om de omgeving `whl` te bouwen/materialiseren.
    + U kunt nu het TLS/SSL-certificaat bijwerken voor het score-eindpunt dat op het AKS-cluster is geïmplementeerd, zowel voor het door Microsoft gegenereerde als het klantcertificaat.
  + **azureml-explain-model**
    + Parameter toegevoegd om een model-id toe te voegen aan uitleg bij het uploaden.
    + Taggen `is_raw` is toegevoegd aan uitleg in het geheugen en uploaden.
    + Pytorch-ondersteuning en -tests toegevoegd voor het pakket azureml-explain-model.
  + **azureml-opendatasets**
    + Ondersteuning voor de testomgeving voor automatische detectie en logboekregistratie.
    + Klassen toegevoegd om de Amerikaanse bevolking op te halen per district en zip.
  + **azureml-pipeline-core**
    + Label-eigenschap toegevoegd aan de definities van de invoer- en uitvoerpoort.
  + **azureml-telemetry**
    + Er is een onjuiste telemetrieconfiguratie opgelost.
  + **azureml-train-automl**
    + Er is een fout opgelost waarbij bij de installatiefout de fout niet werd vastgelegd in het veld 'fouten' voor de installatie-run en dus niet werd opgeslagen in 'fouten' van bovenliggende run.
    + Er is een probleem opgelost in AutoML dat rijen met ontbrekende labels niet juist zijn verwijderd.
    + AutoML-gebruikers toestaan trainingsreeksen te laten vallen die niet lang genoeg zijn bij het voorspellen.
    + AutoML-gebruikers toestaan om bij het voorspellen grains uit de testset te laten vallen die niet in de trainingsset aanwezig zijn.
    + AutoMLStep geeft nu de configuratie door aan de back-end om problemen met wijzigingen of toevoegingen van nieuwe `automl` configuratieparameters te voorkomen.
    + AutoML Data Guardrail is nu beschikbaar als openbare preview. De gebruiker ziet een gegevensbeschermingsrapport (voor classificatie-/regressietaken) na de training en kan er ook toegang toe krijgen via de SDK-API.
  + **azureml-train-core**
    + Er is ondersteuning toegevoegd voor 1.2 in PyTorch Estimator.
  + **azureml-widgets**
    + Verbeterde verwarringsmatrixdiagrammen voor classificatietraining.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **Nieuwe functies**
  + Lijsten met tekenreeksen kunnen nu worden doorgegeven als invoer voor `read_*` methoden.

+ **Opgeloste fouten en verbeteringen**
  + De prestaties van `read_parquet` zijn verbeterd bij het uitvoeren in Spark.
  + Er is een probleem `column_type_builder` opgelost waarbij mislukt in het geval van één kolom met dubbelzinnige datumnota's.

### <a name="azure-portal"></a>Azure Portal
+ **Preview-functie**
  + Het streamen van logboek- en uitvoerbestanden is nu beschikbaar voor pagina's met details van de uitvoer. De bestanden worden in realtime bijgewerkt wanneer de preview-schakelknop is ingeschakeld.
  + De mogelijkheid om een quotum in te stellen op werkruimteniveau wordt uitgebracht in de preview-versie. AmlCompute-quota worden toegewezen op abonnementsniveau, maar we bieden u nu de mogelijkheid om dat quotum tussen werkruimten te distribueren en toe te wijzen voor eerlijk delen en governance. Klik in de **linkernavigatiebalk** van uw werkruimte op de blade Gebruik en quota en selecteer **het tabblad Quota configureren.** U moet een abonnementsbeheerder zijn om quota op werkruimteniveau te kunnen instellen, omdat dit een bewerking tussen werkruimten is.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK voor Python v1.0.55

+ **Nieuwe functies**
  + Verificatie op basis van een token wordt nu ondersteund voor de aanroepen naar het score-eindpunt dat in AKS is geïmplementeerd. We blijven de huidige verificatie op basis van sleutels ondersteunen en gebruikers kunnen een van deze verificatiemechanismen tegelijk gebruiken.
  + Mogelijkheid om een blobopslag die zich achter het virtuele netwerk (VNet) als een gegevensopslag te registreren.

+ **Opgeloste fouten en verbeteringen**
  + **azureml-automl-core**
    + Lost een fout op waarbij de validatiegrootte voor CV-splitsingen klein is en resulteert in slecht voorspelde versus echte grafieken voor regressie en prognoses.
    + De logboekregistratie van prognosetaken op de externe runs is verbeterd. De gebruiker krijgt nu een uitgebreid foutbericht als de run is mislukt.
    + Er zijn fouten van `Timeseries` opgelost als de voorverwerkingsvlag Waar is.
    + Sommige foutberichten voor de validatie van prognoses zijn bruikbaarder geworden.
    + Minder geheugenverbruik van AutoML-runs door gegevenssets te laten vallen en/of traag te laden, met name tussen procesuitspoepen
  + **azureml-contrib-explain-model**
    + Er model_task toegevoegd aan uitlegers, zodat de gebruiker de standaard automatische deferentielogica voor het modeltype kan overschrijven
    + Widgetwijzigingen: wordt automatisch geïnstalleerd met , niet meer installeren/inschakelen - ondersteuning voor uitleg met globaal `contrib` `nbextension` functie-belang (bijvoorbeeld Permutatief)
    + Dashboardwijzigingen: - Boxplots en cirkeldiagrammen naast plot op overzichtspagina - Veel snellere rerendering van plot op `beeswarm` 'Top -k'-schuifregelaarwijziging - handig bericht waarin wordt uitgelegd hoe top-k wordt berekend - Nuttige aanpasbare berichten in plaats van grafieken wanneer er geen gegevens `beeswarm` zijn opgegeven
  + **azureml-core**
    + Methode Model.package() toegevoegd om Docker-afbeeldingen en Docker-bestanden te maken die modellen en hun afhankelijkheden inkapselen.
    + Lokale webservices bijgewerkt om InferenceConfigs met omgevingsobjecten te accepteren.
    + Er is een probleem opgelost met Model.register() dat ongeldige modellen produceert wanneer '.' (voor de huidige map) wordt doorgegeven als de model_path parameter.
    + Voeg Run.submit_child, de functionaliteit spiegelt Experiment.submit terwijl de run wordt opgegeven als het bovenliggende van de verzonden onderliggende run.
    + Ondersteuning voor configuratieopties van Model.register in Run.register_model.
    + Mogelijkheid om JAR-taken uit te voeren op een bestaand cluster.
    + Ondersteunt nu instance_pool_id en cluster_log_dbfs_path parameters.
    + Ondersteuning toegevoegd voor het gebruik van een Environment-object bij het implementeren van een model in een webservice. Het object Environment kan nu worden opgegeven als onderdeel van het inferenceConfig-object.
    + Appinsifht-toewijzing toevoegen voor nieuwe regio's - centralus - westus - northcentralus
    + Documentatie toegevoegd voor alle kenmerken in alle Datastore-klassen.
    + De parameter blob_cache_timeout toegevoegd aan `Datastore.register_azure_blob_container` .
    + Er save_to_directory en load_from_directory toegevoegd aan azureml.core.environment.Environment.
    + De opdrachten az ml environment download en az ml environment register zijn toegevoegd aan de CLI.
    + De Environment.add_private_pip_wheel toegevoegd.
  + **azureml-explain-model**
    + Tracering van gegevenssets toegevoegd aan Explanations met behulp van de gegevenssetservice (preview).
    + Standaardbatchgrootte verlaagd bij het streamen van globale uitleg van 10.000 naar 100.
    + Er model_task toegevoegd aan uitlegers, zodat de gebruiker de standaardlogica voor automatische delegering voor het modeltype kan overschrijven.
  + **azureml-mlflow**
    + Er is een fout opgelost in mlflow.azureml.build_image waarbij geneste directories worden genegeerd.
  + **azureml-pipeline-steps**
    + Mogelijkheid toegevoegd om JAR-taken uit te voeren op Azure Databricks cluster.
    + Ondersteuning toegevoegd instance_pool_id en cluster_log_dbfs_path parameters voor databricksStep-stap.
    + Ondersteuning toegevoegd voor pijplijnparameters in DatabricksStep-stap.
  + **azureml-train-automl**
    + Toegevoegd `docstrings` voor de aan het Ensemble gerelateerde bestanden.
    + Docs bijgewerkt naar geschiktere taal voor `max_cores_per_iteration` en `max_concurrent_iterations`
    + De logboekregistratie van prognosetaken op de externe runs is verbeterd. De gebruiker krijgt nu een uitgebreid foutbericht als de run is mislukt.
    + De get_data verwijderd uit het notebook van de `automlstep` pijplijn.
    + Ondersteuning gestart `dataprep` in `automlstep` .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **Nieuwe functies**
  + U kunt nu aanvragen om specifieke inspectors (bijvoorbeeld histogram, spreidingsdiagram, enzovoort) uit te voeren op specifieke kolommen.
  + Er is een argument voor parallelliseren toegevoegd aan `append_columns` . Indien waar, worden gegevens in het geheugen geladen, maar wordt de uitvoering parallel uitgevoerd; Als deze false is, wordt de uitvoering gestreamd, maar met één thread.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK voor Python v1.0.53

+ **Nieuwe functies**
  + Geautomatiseerde Machine Learning ondersteunt nu het trainen van ONNX-modellen op het externe rekendoel
  + Azure Machine Learning biedt nu de mogelijkheid om de training te hervatten vanuit een eerdere uitvoering, controlepunt of modelbestanden.
    + Meer informatie over het [gebruik van estimators om de training van een vorige run te hervatten](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Opgeloste fouten en verbeteringen**
  + **azure-cli-ml**
    + CLI-opdrachten 'model deploy' en 'service update' accepteren nu parameters, configuratiebestanden of een combinatie van de twee. Parameters hebben voorrang op kenmerken in bestanden.
    + De modelbeschrijving kan nu worden bijgewerkt na de registratie
  + **azureml-automl-core**
    + NimbusML-afhankelijkheid bijgewerkt naar versie 1.2.0 (nieuwste versie).
    + Ondersteuning toegevoegd voor NimbusML-estimators & pijplijnen die moeten worden gebruikt in AutoML-estimators.
    + Het oplossen van een fout in de selectieprocedure voor ensembles die het resulterende ensemble onnodig heeft laten groeien, zelfs als de scores constant blijven.
    + Schakel hergebruik van sommige featurizations in cv-splitsingen in voor prognosetaken. Dit versnelt de uitvoering van de installatie met ongeveer een factor van n_cross_validations voor dure featurizations zoals vertragingen en doorloopvensters.
    + Een probleem oplossen als de tijd buiten het door pandas ondersteunde tijdsbereik valt. We geven nu een DataException weer als de tijd minder is dan pd. Timestamp.min of groter dan pd. Timestamp.max
    + Prognoses maken nu verschillende frequenties mogelijk in de sets voor trainen en testen als ze kunnen worden uitgelijnd. Zo kunnen 'driemaandelijks beginnen in januari' en 'elk kwartaal vanaf oktober' worden uitgelijnd.
    + De eigenschap 'parameters' is toegevoegd aan de TimeSeriesTransformer.
    + Verwijder oude uitzonderingsklassen.
    + Bij prognosetaken `target_lags` accepteert de parameter nu één geheel getal of een lijst met gehele getallen. Als het gehele getal is opgegeven, wordt slechts één vertraging gemaakt. Als er een lijst wordt opgegeven, worden de unieke waarden voor vertragingen gebruikt. target_lags=[1, 2, 2, 4] creëert vertragingen van één, twee en vier perioden.
    + Los de fout op over het verlies van kolommentypen na de transformatie (gekoppelde fout);
    + In staat u y_query een objecttype te zijn dat `model.forecast(X, y_query)` Geen(en) aan het begin bevat (#459519).
    + Verwachte waarden toevoegen aan `automl` uitvoer
  + **azureml-contrib-datadrift**
    +  Verbeteringen in voorbeeldnotenote met inbegrip van overschakelen naar azureml-opendatasets in plaats van azureml-contrib-opendatasets en prestatieverbeteringen bij het verrijken van gegevens
  + **azureml-contrib-explain-model**
    + Probleem opgelost met argument voor transformaties voor DEP-uitleg voor onbewerkte functie-belang in het pakket azureml-contrib-explain-model
    + Segmentaties toegevoegd aan afbeeldings uitleg in afbeeldings uitleg voor het pakket AzureML-contrib-explain-model
    + Ondersteuning voor scipy sparse toevoegen voor MoetExplainer
    + Toegevoegd om uitleg te bootsen wanneer , voor het streamen van globale uitleg in batches om de uitvoeringstijd van `batch_size` `include_local=False` DecisionTreeExplainableModel te verbeteren
  + **azureml-contrib-featureengineering**
    + Oplossing voor het aanroepen set_featurizer_timeseries_params(): wijziging van dictwaardetype en null-controle - Notebook toevoegen voor `timeseries`  featurizer
    + NimbusML-afhankelijkheid bijgewerkt naar versie 1.2.0 (nieuwste versie).
  + **azureml-core**
    + De mogelijkheid toegevoegd om DBFS-gegevensstores te koppelen in de AzureML CLI
    + De fout met het uploaden van gegevensstores waarbij een lege map wordt gemaakt, is opgelost als deze `target_path` is gestart met `/`
    + Probleem `deepcopy` opgelost in ServicePrincipalAuthentication.
    + De opdrachten az ml environment show en az ml environment list zijn toegevoegd aan de CLI.
    + Omgevingen ondersteunen nu het opgeven van een base_dockerfile als alternatief voor een al gebouwde base_image.
    + De instelling voor ongebruikte RunConfiguration auto_prepare_environment is gemarkeerd als afgeschaft.
    + De modelbeschrijving kan nu worden bijgewerkt na de registratie
    + Bugfix: Model en afbeelding verwijderen biedt nu meer informatie over het ophalen van upstream-objecten die ervan afhankelijk zijn als verwijderen mislukt vanwege een upstream-afhankelijkheid.
    + Er is een fout opgelost die een lege duur heeft afgedrukt voor implementaties die optreden bij het maken van een werkruimte voor bepaalde omgevingen.
    + Verbeterde fout-uitzonderingen voor het maken van werkruimten. Dit betekent dat gebruikers 'Kan werkruimte niet maken' niet zien. Kan niet vinden... als het bericht en in plaats daarvan ziet u de werkelijke fout bij het maken.
    + Ondersteuning voor tokenverificatie toevoegen in AKS-webservices.
    + Methode `get_token()` toevoegen aan `Webservice` objecten.
    + CLI-ondersteuning toegevoegd voor het beheren machine learning gegevenssets.
    + `Datastore.register_azure_blob_container` neemt nu desgewenst een waarde (in seconden) waarmee de parameters voor het toevoegen van blobfuse worden geconfigureerd om verloop van de cache voor dit `blob_cache_timeout` gegevensopslag mogelijk te maken. De standaardwaarde is geen time-out, bijvoorbeeld wanneer een blob wordt gelezen. Deze blijft in de lokale cache totdat de taak is voltooid. De meeste taken geven de voorkeur aan deze instelling, maar sommige taken moeten meer gegevens uit een grote gegevensset lezen dan op hun knooppunten passen. Voor deze taken helpt het afstemmen van deze parameter om ze te helpen slagen. Zorg ervoor dat u deze parameter afstemt: het instellen van de waarde te laag kan leiden tot slechte prestaties, omdat de gegevens die in een tijdvak worden gebruikt, kunnen verlopen voordat ze opnieuw worden gebruikt. Alle leesingen worden uitgevoerd vanuit blobopslag/-netwerk in plaats van de lokale cache, wat een negatieve invloed heeft op de trainingstijden.
    + De modelbeschrijving kan nu correct worden bijgewerkt na de registratie
    + Het verwijderen van modellen en afbeeldingen biedt nu meer informatie over upstream-objecten die ervan afhankelijk zijn, waardoor het verwijderen mislukt
    + Verbeter het resourcegebruik van externe runs met behulp van azureml.mlflow.
  + **azureml-explain-model**
    + Argument voor transformaties voor DEP-uitleg voor onbewerkte functie-belang in het pakket azureml-contrib-explain-model opgelost
    + scipy sparse-ondersteuning voor MoetExplainer toevoegen
    + shape linear explainer wrapper toegevoegd, evenals een ander niveau aan tabellaire uitleg voor het uitleggen van lineaire modellen
    + for mimic explainer in explain model library, fixed error when include_local=False for sparse data input
    + verwachte waarden toevoegen aan `automl` uitvoer
    + probleem opgelost met het belang van permutatiefunctie bij het opgegeven argument voor transformaties om het belang van onbewerkte functies op te halen
    + toegevoegd aan mimic explainer wanneer , voor het streamen van globale uitleg in batches om de uitvoeringstijd van `batch_size` `include_local=False` DecisionTreeExplainableModel te verbeteren
    + voor de bibliotheek voor model verklarende gegevens, vaste blackbox-uitleg waar pandas-gegevensframeinvoer vereist is voor voorspelling
    + Er is een fout opgelost `explanation.expected_values` waarbij soms een float zou retourneren in plaats van een lijst met een float.
  + **azureml-mlflow**
    + Prestaties van mlflow.set_experiment (experiment_name)
    + Er is een fout opgelost in het gebruik van InteractiveLoginAuthentication voor mlflow-tracking_uri
    + Verbeter het resourcegebruik van externe runs met behulp van azureml.mlflow.
    + De documentatie van het pakket azureml-mlflow verbeteren
    + Patch bug where mlflow.log_artifacts("my_dir") would save artifacts under "my_dir/<artifact-paths>" in plaats van "<artifact-paths>"
  + **azureml-opendatasets**
    + Pincode `pyarrow` van naar oude versies `opendatasets` (<0.14.0) vanwege een geheugenprobleem dat daar zojuist is geïntroduceerd.
    + Verplaats azureml-contrib-opendatasets naar azureml-opendatasets.
    + Toestaan dat open gegevenssetklassen worden geregistreerd bij Azure Machine Learning werkruimte en naadloos gebruikmaken van de mogelijkheden van de AML-gegevensset.
    + Verbeter NoaaIsdWeather verrijk de prestaties aanzienlijk in een niet-SPARK-versie.
  + **azureml-pipeline-steps**
    + DBFS-gegevensstore wordt nu ondersteund voor invoer en uitvoer in DatabricksStep.
    + Bijgewerkte documentatie voor Azure Batch stap met betrekking tot invoer/uitvoer.
    + In AzureBatchStep is de *standaardwaarde delete_batch_job_after_finish* gewijzigd in *true.*
  + **azureml-telemetry**
    +  Verplaats azureml-contrib-opendatasets naar azureml-opendatasets.
    + Toestaan dat open gegevenssetklassen worden geregistreerd bij Azure Machine Learning werkruimte en naadloos gebruikmaken van de mogelijkheden van de AML-gegevensset.
    + Verbeter NoaaIsdWeather verrijk de prestaties aanzienlijk in een niet-SPARK-versie.
  + **azureml-train-automl**
    + Bijgewerkte documentatie over get_output met het werkelijke retourtype en geef aanvullende notities over het ophalen van sleuteleigenschappen.
    + NimbusML-afhankelijkheid bijgewerkt naar versie 1.2.0 (nieuwste versie).
    + verwachte waarden toevoegen aan `automl` uitvoer
  + **azureml-train-core**
    + Tekenreeksen worden nu geaccepteerd als rekendoel voor geautomatiseerd afstemmen van hyperparameters
    + De instelling ongebruikte RunConfiguration auto_prepare_environment is gemarkeerd als afgeschaft.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **Nieuwe functies**
  + Ondersteuning toegevoegd voor het lezen van een bestand rechtstreeks vanuit een HTTP- of HTTPS-URL.

+ **Opgeloste fouten en verbeteringen**
  + Verbeterd foutbericht bij het lezen van een Parquet-gegevensset uit een externe bron (die momenteel niet wordt ondersteund).
  + Er is een fout opgelost bij het schrijven naar de Parquet-bestandsindeling in ADLS Gen 2 en het bijwerken van de naam van de ADLS Gen 2-container in het pad.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Visuele interface
+ **Preview-functies**
  + De module 'R-script uitvoeren' is toegevoegd aan de visuele interface.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK voor Python v1.0.48

+ **Nieuwe functies**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** is nu beschikbaar als **azureml-opendatasets.** Het oude pakket kan nog steeds werken, maar we raden u aan **om azureml-opendatasets** te gebruiken voor uitgebreidere mogelijkheden en verbeteringen.
    + Met dit nieuwe pakket kunt u open gegevenssets registreren als gegevensset in Azure Machine Learning werkruimte en gebruikmaken van de functies die Dataset biedt.
    + Het bevat ook bestaande mogelijkheden, zoals het gebruiken van open gegevenssets als Pandas-/SPARK-gegevensframes en locatie-joins voor een bepaalde gegevensset, zoals het weer.

+ **Preview-functies**
    + HyperDriveConfig kan nu pijplijnobject accepteren als een parameter ter ondersteuning van het afstemmen van hyperparameters met behulp van een pijplijn.

+ **Opgeloste fouten en verbeteringen**
  + **azureml-train-automl**
    + Probleem opgelost met het verlies van kolommentypen na de transformatie.
    + De fout is opgelost zodat y_query objecttype aan het begin geen(en) bevat.
    + Het probleem in de selectieprocedure voor het ensemble opgelost, waardoor het resulterende ensemble onnodig werd geconfigeerd, zelfs als de scores constant waren.
    + Probleem opgelost met de instellingen list_models toestaan en list_models blokkeren in AutoMLStep.
    + Er is een probleem opgelost waardoor het gebruik van voorverwerking niet mogelijk was wanneer AutoML zou worden gebruikt in de context van Azure ML Pipelines.
  + **azureml-opendatasets**
    + Azureml-contrib-opendatasets verplaatst naar azureml-opendatasets.
    + Toegestane open-gegevenssetklassen kunnen worden geregistreerd Azure Machine Learning werkruimte en naadloos gebruikmaken van de mogelijkheden van AML-gegevenssets.
    + Verbeterde NoaaIsdWeather verrijk de prestaties in niet-SPARK-versies aanzienlijk.
  + **azureml-explain-model**
    + Onlinedocumentatie bijgewerkt voor interpreteerbaarheidsobjecten.
    + Toegevoegd om uitleg na te bootsen wanneer , voor het streamen van globale uitleg in batches om de uitvoeringstijd van `batch_size` DecisionTreeExplainableModel te verbeteren voor de bibliotheek voor `include_local=False` modeluit verklaarbaarheid.
    + Het probleem opgelost waarbij `explanation.expected_values` soms een float retourneerde in plaats van een lijst met een float.
    + Verwachte waarden toegevoegd aan uitvoer `automl` voor mimic explainer in de uitlegmodelbibliotheek.
    + Probleem opgelost met permutatiefunctie-belang bij opgegeven transformatiesargument om onbewerkte functie-belang te krijgen.
  + **azureml-core**
    + De mogelijkheid toegevoegd om DBFS-gegevensstores te koppelen in de AzureML CLI.
    + Er is een probleem opgelost met het uploaden van een gegevensstore waarbij een lege map wordt gemaakt als deze `target_path` is gestart met `/` .
    + Vergelijking van twee gegevenssets ingeschakeld.
    + Model en afbeelding verwijderen biedt nu meer informatie over het ophalen van upstream-objecten die ervan afhankelijk zijn als verwijderen mislukt vanwege een upstream-afhankelijkheid.
    + De niet-gebruikte RunConfiguration-instelling in de auto_prepare_environment.
  + **azureml-mlflow**
    + Verbeterd resourcegebruik van externe runs die gebruikmaken van azureml.mlflow.
    + Verbeterde documentatie van het pakket azureml-mlflow.
    + Het probleem opgelost waarbij mlflow.log_artifacts("my_dir") artefacten opsla in 'my_dir/artifact-paths' in plaats van 'artifact-paths'.
  + **azureml-pipeline-core**
    + Parameter hash_paths voor alle pijplijnstappen is afgeschaft en wordt in de toekomst verwijderd. Standaard wordt inhoud van de source_directory gehasht (met uitzondering van bestanden die worden vermeld in `.amlignore` of `.gitignore` )
    + Module en ModuleStep blijven verbeteren ter ondersteuning van rekentypespecifieke modules, ter voorbereiding op RunConfiguration-integratie en andere wijzigingen voor het ontgrendelen van het gebruik van rekentypemodules in pijplijnen.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Verbeterde documentatie met betrekking tot invoer/uitvoer.
    + AzureBatchStep: de standaardwaarde delete_batch_job_after_finish gewijzigd in true.
  + **azureml-train-core**
    + Tekenreeksen worden nu geaccepteerd als rekendoel voor Automated Hyperparameter Tuning.
    + De niet-gebruikte RunConfiguration-instelling in de auto_prepare_environment.
    + Parameters afgeschaft en `conda_dependencies_file_path` `pip_requirements_file_path` in de plaats van respectievelijk `conda_dependencies_file` en `pip_requirements_file` .
  + **azureml-opendatasets**
    + Verbeter NoaaIsdWeather en verrijk de prestaties in niet-SPARK-versies aanzienlijk.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK voor Python v1.0.33 uitgebracht.

+ Azure ML Modellen met hardwareversnelling op [FPGA's](how-to-deploy-fpga-web-service.md) is algemeen beschikbaar.
  + U kunt nu [het pakket azureml-accel-models gebruiken voor](how-to-deploy-fpga-web-service.md) het volgende:
    + Het gewicht trainen van een ondersteund deep neural netwerk (ResNet 50, ResNet 152, DenseNet-121, VGG-16 en SSD-VGG)
    + Leeroverdracht gebruiken met de ondersteunde DNN
    + Het model registreren bij Modelbeheer Service en het model in een container zetten
    + Het model implementeren op een Azure-VM met een FPGA in een Azure Kubernetes Service (AKS)-cluster
  + De container implementeren op een [Azure Data Box Edge-serverapparaat](../databox-online/azure-stack-edge-overview.md)
  + Uw gegevens scoren met het gRPC-eindpunt met dit [voorbeeld](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Geautomatiseerde machine learning

+ Functies kunnen dynamisch worden toegevoegd voor :::no-loc text="featurizers"::: prestatieoptimalisatie. Nieuw: :::no-loc text="featurizers"::: werkinsluiten, gewicht van bewijs, doelcodeeringen, codering van tekstdoel, clusterafstand
+ Slim CV voor het afhandelen van train-/geldige splitsingen binnen geautomatiseerde ML
+ Weinig wijzigingen in geheugenoptimalisatie en prestatieverbetering van runtime
+ Uitleg over prestatieverbetering in model
+ ONNX-modelconversie voor lokale uitvoering
+ Ondersteuning voor subsampling toegevoegd
+ Intelligent stoppen wanneer er geen exitcriteria zijn gedefinieerd
+ Gestapelde ensembles

+ Tijdreeksvoorspelling
  + Nieuwe voorspellingsfunctie
  + U kunt nu rolling-origin kruisvalidatie gebruiken voor tijdreeksgegevens
  + Nieuwe functionaliteit toegevoegd om vertragingen in tijdreeksen te configureren
  + Er is nieuwe functionaliteit toegevoegd ter ondersteuning van functies voor het samenvoegen van rolling-vensters
  + Nieuwe vakantiedetectie en featurizer wanneer landcode wordt gedefinieerd in experimentinstellingen

+ Azure Databricks
  + Ingeschakelde prognose van tijdreeksen en mogelijkheden voor modeluit leggenabiliteit/interpreteerbaarheid
  + U kunt nu geautomatiseerde ML-experimenten annuleren en hervatten (doorgaan)
  + Ondersteuning toegevoegd voor verwerking met meerdere kernen

### <a name="mlops"></a>MLOps
+ **Lokale implementatie & voor scorende containers**<br/> U kunt nu een ML-model lokaal implementeren en snel itereren op uw scorebestand en afhankelijkheden om ervoor te zorgen dat ze naar verwachting werken.

+ **InferenceConfig geïntroduceerd & Model.deploy()**<br/> Modelimplementatie ondersteunt nu het opgeven van een bronmap met een invoerscript, hetzelfde als een RunConfig.  Daarnaast is de modelimplementatie vereenvoudigd tot één opdracht.

+ **Git-verwijzing bijhouden**<br/> Klanten vragen al enige tijd om basismogelijkheden voor Git-integratie, omdat het helpt om een volledig audittrail bij te houden. We hebben tracering geïmplementeerd voor belangrijke entiteiten in Azure ML voor Git-gerelateerde metagegevens (repo, commit, clean state). Deze informatie wordt automatisch verzameld door de SDK en CLI.

+ **Modelprofilering & validatieservice**<br/> Klanten geven vaak aan dat het lastig is om de rekenkracht van hun deferentieservice op de juiste manier te berekenen. Met onze service voor modelprofilering kan de klant voorbeeldinvoer leveren en wordt geprofileert in 16 verschillende CPU-/geheugenconfiguraties om de optimale schaal voor implementatie te bepalen.

+ **Bring Your Own Base-afbeelding voor de deferentie**<br/> Een andere veelvoorkomende klachten was het probleem bij het over te gaan van experimenten naar deference RE sharing-afhankelijkheden. Met onze nieuwe functie voor het delen van basisafbeeldingen kunt u nu uw basisafbeeldingen, afhankelijkheden en alle experimenten opnieuw gebruiken voor de deferatie. Dit moet de implementaties versnellen en de hiaat van de binnenste naar de buitenste lus verkleinen.

+ **Verbeterde ervaring voor het genereren van Swagger-schema's**<br/> Onze vorige methode voor het genereren van Swagger is foutgevoelig en kan niet worden automatiseren. We hebben een nieuwe in line-way voor het genereren van swagger-schema's op basis van een Python-functie via aandelers. We hebben deze code open source gemaakt en ons protocol voor het genereren van schema's is niet gekoppeld aan het Azure ML-platform.

+ **Azure ML CLI algemeen beschikbaar (GA)**<br/> Modellen kunnen nu worden geïmplementeerd met één CLI-opdracht. We hebben algemene feedback van klanten gekregen dat niemand een ML-model implementeert vanuit een Jupyter-notebook. De [**CLI-referentiedocumentatie**](./reference-azure-machine-learning-cli.md) is bijgewerkt.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK voor Python v1.0.30 uitgebracht.

De is geïntroduceerd om een nieuwe versie van een gepubliceerde pijplijn [`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint) toe te voegen terwijl hetzelfde eindpunt behouden blijft.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + U kunt nu een bestaand script dat wordt uitgevoerd op een bestaand extern rekencluster opnieuw inzenden.
  + U kunt nu een gepubliceerde pijplijn met nieuwe parameters uitvoeren op het tabblad Pijplijnen.
  + Details van de run ondersteunen nu een nieuwe Viewer voor momentopnamen. U kunt een momentopname van de map weergeven wanneer u een specifieke run hebt verzonden. U kunt ook het notebook downloaden dat is verzonden om de run te starten.
  + U kunt nu bovenliggende runs annuleren vanuit de Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK voor Python v1.0.23

+ **Nieuwe functies**
  + De Azure Machine Learning SDK ondersteunt nu Python 3.7.
  + Azure Machine Learning DNN-estimators bieden nu ingebouwde ondersteuning voor meerdere versies. Estimator accepteert nu bijvoorbeeld een parameter en gebruikers kunnen versie `TensorFlow` `framework_version` 1.10 of 1.12 opgeven. Voor een lijst met de versies die worden ondersteund door uw huidige SDK-release roept u de gewenste `get_supported_versions()` frameworkklasse aan (bijvoorbeeld `TensorFlow.get_supported_versions()` ).
  Zie de [DNN Estimator-documentatie](/python/api/azureml-train-core/azureml.train.dnn)voor een lijst met de versies die worden ondersteund door de nieuwste SDK-release.

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK voor Python v1.0.21

+ **Nieuwe functies**
  + Met *azureml.core.Run.create_children* methode kunt u met een lage latentie meerdere onderliggende runs maken met één aanroep.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK voor Python v1.0.18

 + **Wijzigingen**
   + Het pakket azureml-tensorboard vervangt azureml-contrib-tensorboard.
   + Met deze release kunt u tijdens het maken een gebruikersaccount instellen op uw beheerde rekencluster (amlcompute). Dit kan worden gedaan door deze eigenschappen door te geven in de inrichtingsconfiguratie. Meer informatie vindt u in de [SDK-referentiedocumentatie](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Nieuwe functies**
  + Ondersteunt nu het toevoegen van twee numerieke kolommen om een resultaatkolom te genereren met behulp van de expressietaal.

+ **Opgeloste fouten en verbeteringen**
  + Verbeterde documentatie en parametercontrole voor random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Opgeloste fout**
  + Er is een verificatieprobleem met een service-principal opgelost dat werd veroorzaakt door een API-wijziging.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK voor Python v1.0.17

+ **Nieuwe functies**
  + Azure Machine Learning biedt nu eersteklas ondersteuning voor populaire DNN Framework Chainer. Met [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer) klassegebruikers kunnen Chainer-modellen eenvoudig trainen en implementeren.
    + Meer informatie over het [uitvoeren van gedistribueerde training met ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)
    + Meer informatie over het [uitvoeren van hyperparameter-afstemming met Chainer met behulp van HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning Pijplijnen is de mogelijkheid toegevoegd om een pijplijn-run te activeren op basis van wijzigingen in het gegevens opslaan. Het notebook voor [pijplijnplanning](https://aka.ms/pl-schedule) is bijgewerkt om deze functie te presenteren.

+ **Opgeloste fouten en verbeteringen**
  + We hebben ondersteuning toegevoegd in Azure Machine Learning-pijplijnen voor het instellen van de eigenschap source_directory_data_store op een gewenste gegevensopslag (zoals een blobopslag) op [RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration) die worden geleverd aan [de PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep). Stappen gebruiken standaard Azure File Store als het gegevensopslag voor back-overstappen, wat kan leiden tot beperkingsproblemen wanneer een groot aantal stappen gelijktijdig wordt uitgevoerd.

### <a name="azure-portal"></a>Azure Portal

+ **Nieuwe functies**
  + Nieuwe ervaring voor tabeleditor voor rapporten met slepen en neerzetten. Gebruikers kunnen een kolom van de well naar het tabelgebied slepen waar een voorbeeld van de tabel wordt weergegeven. De kolommen kunnen opnieuw worden gerangeerd.
  + Nieuwe logboekenbestandsviewer
  + Koppelingen naar uitvoeringen, berekeningen, modellen, afbeeldingen en implementaties van het tabblad Activiteiten

## <a name="next-steps"></a>Volgende stappen

Lees het overzicht voor [Azure Machine Learning](overview-what-is-azure-ml.md).

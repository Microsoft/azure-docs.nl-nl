---
title: 'ML Studio (klassiek): een webservice implementeren-Azure'
description: Een trainings experiment converteren naar een voorspellend experiment, het voorbereiden voor implementatie en het vervolgens implementeren als een Azure Machine Learning Studio-webservice (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 018e292abc7050f5d71c6bf9065618fecf8b5668
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520439"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Een Azure Machine Learning Studio-webservice (klassiek) implementeren

**VAN TOEPASSING OP:**  ![Dit is een vinkje, wat betekent dat dit artikel van toepassing is op Machine Learning Studio (klassiek).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek)   ![Dit is een X, wat betekent dat dit artikel van toepassing is op Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Met Azure Machine Learning Studio (klassiek) kunt u een voorspellende analyse oplossing bouwen en testen. Vervolgens kunt u de oplossing implementeren als een webservice.

Machine Learning Studio (Classic) webservices bieden een interface tussen een toepassing en een score model voor het maken van een Machine Learning Studio (klassiek) werk stroom. Een externe toepassing kan in realtime communiceren met een werk stroom score model voor Machine Learning Studio (klassiek). Een aanroep van een Machine Learning Studio (klassieke) webservice retourneert Voorspellings resultaten naar een externe toepassing. Tijdens een aanroep aan een webservice wordt een API-sleutel doorgegeven die is gemaakt tijdens de implementatie van de webservice. Een webservice Machine Learning Studio (klassiek) is gebaseerd op REST, een populaire architectuur voor webprogrammeer projecten.

Azure Machine Learning Studio (klassiek) heeft twee soorten webservices:

* Request-Response service (RR'S): een zeer schaal bare service met een lage latentie die één gegevens record verdeelt.
* Batch Execution Service (BES): een asynchrone service die een batch met gegevens records verlaagt.

De invoer voor BES is net als de gegevensinvoer die door RRS wordt gebruikt. Het belangrijkste verschil is dat BES een blok records uit diverse bronnen leest, zoals Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (hive query) en HTTP-bronnen.

Vanuit een hoogwaardige punt weergave implementeert u uw model in drie stappen:

* **[Een trainings experiment maken]** : in Studio (klassiek) kunt u een Predictive Analytics model trainen en testen met behulp van de trainings gegevens die u opgeeft, met behulp van een groot aantal ingebouwde machine learning algoritmen.
* **[Converteren naar een voorspellend experiment]** : Zodra uw model is getraind met bestaande gegevens en u klaar bent om het te gebruiken voor het beoordelen van nieuwe gegevens, kunt u uw experiment voorbereiden en stroom lijnen voor voor spellingen.
* **Implementeren** als een **[nieuwe webservice]** of een **[klassieke webservice]** : wanneer u uw voorspellende experiment implementeert als een Azure-webservice, kunnen gebruikers gegevens naar uw model verzenden en de voor spellingen van uw model ontvangen.

## <a name="create-a-training-experiment"></a>Een trainings experiment maken

Als u een predictive analytics model wilt trainen, gebruikt u Azure Machine Learning Studio (klassiek) om een trainings experiment te maken waarin u diverse modules opneemt voor het laden van trainings gegevens, het voorbereiden van de gegevens, het Toep assen van machine learning algoritmen en het evalueren van de resultaten. U kunt een experiment herhalen en verschillende machine learning algoritmen proberen om de resultaten te vergelijken en te evalueren.

Het proces voor het maken en beheren van trainings experimenten wordt uitgebreid besproken. Raadpleeg deze artikelen voor meer informatie:

* [Een eenvoudig experiment maken in Azure Machine Learning Studio (klassiek)](create-experiment.md)
* [Een voorspellende oplossing ontwikkelen met Azure Machine Learning Studio (klassiek)](tutorial-part1-credit-risk.md)
* [Uw trainings gegevens importeren in Azure Machine Learning Studio (klassiek)](import-data.md)
* [Experiment herhalingen beheren in Azure Machine Learning Studio (klassiek)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Het trainingsexperiment converteren naar een voorspellend experiment

Zodra u uw model hebt getraind, kunt u uw trainings experiment converteren naar een voorspellend experiment om nieuwe gegevens te scoren.

Door te converteren naar een voorspellend experiment, krijgt u het getrainde model klaar om te worden geïmplementeerd als een score-webservice. Gebruikers van de webservice kunnen invoer gegevens naar uw model verzenden en uw model stuurt de Voorspellings resultaten terug. Wanneer u converteert naar een voorspellend experiment, moet u er rekening mee houden dat u verwacht dat uw model door anderen wordt gebruikt.

Het proces voor het converteren van een opleidings experiment naar een voorspellend experiment bestaat uit drie stappen:

1. Vervang de machine learning algoritme modules door uw getrainde model.
2. Knip het experiment alleen uit op de modules die nodig zijn om te scoren. Een trainings experiment bevat een aantal modules die nodig zijn voor de training, maar die niet nodig zijn wanneer het model is getraind.
3. Definiëren hoe uw model gegevens van de webservice-gebruiker accepteert en welke gegevens worden geretourneerd.

> [!TIP]
> In uw trainings experiment hebt u zich met uw eigen gegevens getraind om uw model te trainen en te scoren. Maar na de implementatie worden gebruikers nieuwe gegevens naar uw model verzonden en retour neren ze de Voorspellings resultaten. Wanneer u uw trainings experiment converteert naar een voorspellend experiment om het klaar te maken voor implementatie, houdt u er rekening mee dat het model door anderen wordt gebruikt.

![Converteren naar Score experiment](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>Knop webservice instellen
Nadat u uw experiment hebt uitgevoerd (Klik onder aan het canvas op **uitvoeren** ), klikt u op de knop **webservice instellen** (Selecteer de optie **voorspellende webservice** ). Het instellen van de **webservice** voert de volgende drie stappen uit om uw trainings experiment te converteren naar een voorspellend experiment:

1. U kunt uw getrainde model opslaan in de sectie **getrainde modellen** van het module palet (links van het canvas op het experiment). Vervolgens wordt het algoritme voor machine learning en [Train model][train-model] modules vervangen door het opgeslagen getrainde model.
2. Het analyseert uw experiment en verwijdert modules die duidelijk waren gebruikt voor trainingen en die niet meer nodig zijn.
3. Hiermee worden de modules _Web Service input_ en _output_ ingevoegd op de standaard locaties in uw experiment (deze modules accepteren en retour neren gebruikers gegevens).

Het volgende experiment traint bijvoorbeeld een gestimuleerd beslissings structuur model met twee klassen met voorbeeld gegevens over de telling:

![Trainings experiment](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

De modules in dit experiment voeren vier verschillende functies uit:

![Module functies](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Wanneer u dit trainings experiment converteert naar een voorspellend experiment, zijn sommige van deze modules niet meer nodig of hebben ze nu een ander doel:

* **Gegevens** -de gegevens in deze voorbeeld gegevensset worden niet gebruikt tijdens de score-de gebruiker van de webservice levert de gegevens die moeten worden beoordeeld. De meta gegevens van deze gegevensset, zoals gegevens typen, worden echter gebruikt door het getrainde model. Daarom moet u de gegevensset in het voorspellende experiment blijven staan zodat deze meta gegevens kan bevatten.

* **Prep** : afhankelijk van de gebruikers gegevens die worden verzonden voor de score, zijn deze modules mogelijk niet nodig om de binnenkomende gegevens te verwerken. Met de knop **webservice instellen** kunt u deze niet aanraken. u moet bepalen hoe u deze wilt verwerken.
  
    In dit voor beeld kan de gegevensset van de voor beelden bijvoorbeeld ontbrekende waarden bevatten, zodat er een [schone ontbrekende gegevens][clean-missing-data] module werd opgenomen om deze te verwerken. De voorbeeld gegevensset bevat ook kolommen die niet nodig zijn voor het trainen van het model. Daarom is een [select columns in dataset][select-columns] -module opgenomen om deze extra kolommen uit te sluiten van de gegevens stroom. Als u weet dat de gegevens die via de webservice worden verzonden, geen ontbrekende waarden bevatten, kunt u de module [clean Missing Data][clean-missing-data] verwijderen. Omdat in de module [select columns in dataset][select-columns] de kolommen met gegevens worden gedefinieerd die het getrainde model verwacht, moet die module echter behouden blijven.

* **Train** : deze modules worden gebruikt om het model te trainen. Wanneer u op **webservice instellen** klikt, worden deze modules vervangen door één module die het model bevat dat u hebt getraind. Deze nieuwe module wordt opgeslagen in de sectie **getrainde modellen** van het module palet.

* **Score** : in dit voor beeld wordt de module [Split data][split] gebruikt om de gegevens stroom te verdelen in test gegevens en trainings gegevens. In het voorspellende experiment worden we niet meer getraind, zodat [gesplitste gegevens][split] kunnen worden verwijderd. Op dezelfde manier worden de module voor het tweede [score model][score-model] en de [model module evalueren][evaluate-model] gebruikt voor het vergelijken van de resultaten van de test gegevens. deze modules zijn dus niet nodig in het voorspellende experiment. De resterende [score model][score-model] module is echter nodig om een score resultaat te retour neren via de webservice.

Hier ziet u hoe het voor beeld eruitziet nadat **u op webservice instellen** hebt geklikt:

![Geconverteerd predictief experiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Het werk dat is gedaan door de webservice in te **stellen** kan voldoende zijn om uw experiment voor te bereiden om te worden geïmplementeerd als een webservice. Het kan echter ook voor komen dat u meer specifiek werk wilt doen voor uw experiment.

#### <a name="adjust-input-and-output-modules"></a>Invoer-en uitvoer modules aanpassen
In uw trainings experiment hebt u een set trainings gegevens gebruikt en hebt u vervolgens een aantal bewerkingen uitgevoerd om de gegevens op te halen in een formulier waarvan het machine learning algoritme nodig is. Als de gegevens die u verwacht via de webservice, deze verwerking niet nodig hebt, kunt u deze omzeilen: Verbind de uitvoer van de **module Web Service-invoer** naar een andere module in uw experiment. De gegevens van de gebruiker worden nu in het model op deze locatie bezorgd.

Zo wordt bijvoorbeeld standaard de webservice- **invoer** module aan de bovenkant van de gegevens stroom **ingesteld** , zoals wordt weer gegeven in de bovenstaande afbeelding. Maar we kunnen de webservice- **invoer** na de modules voor gegevens verwerking hand matig plaatsen:

![De webservice-invoer verplaatsen](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

De invoer gegevens die via de webservice worden opgegeven, worden nu direct door gegeven in de module score model, zonder voor verwerking.

Op dezelfde manier wordt de webservice-uitvoer module aan de onderkant van de gegevens stroom standaard **ingesteld** . In dit voor beeld keert de webservice terug naar de gebruiker de uitvoer van de module [score model][score-model] , die de volledige invoer gegevens vector bevat plus de resultaten van de score.
Als u echter liever iets anders retourneert, kunt u aanvullende modules toevoegen vóór de module **webservice-uitvoer** . 

Als u bijvoorbeeld alleen de Score resultaten en niet de volledige vector van invoer gegevens wilt retour neren, voegt u een [select columns in dataset][select-columns] -module toe om alle kolommen uit te sluiten, met uitzonde ring van de Score resultaten. Verplaats vervolgens de module **webservice-uitvoer** naar de uitvoer van de module [select columns in dataset][select-columns] . Het experiment ziet er als volgt uit:

![De webservice-uitvoer verplaatsen](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>Extra gegevens verwerkings modules toevoegen of verwijderen
Als uw experiment meer modules bevat waarvan u weet dat ze niet nodig zijn tijdens de score, kunnen deze worden verwijderd. Omdat we de module **webservice-invoer** bijvoorbeeld hebben verplaatst naar een punt na de modules voor gegevens verwerking, kunnen we de module [clean Missing Data][clean-missing-data] uit het voorspellende experiment verwijderen.

Ons voorspellende experiment ziet er nu als volgt uit:

![Extra module verwijderen](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>Optionele webservice-para meters toevoegen
In sommige gevallen wilt u mogelijk de gebruiker van uw webservice toestaan het gedrag van modules te wijzigen wanneer de service wordt geopend. U kunt dit doen met de *para meters* van de webservice.

Een voor beeld hiervan is het instellen van een [import gegevens][import-data] module, zodat de gebruiker van de geïmplementeerde webservice een andere gegevens bron kan opgeven wanneer de webservice wordt geopend. Of configureer een module voor het [exporteren van gegevens][export-data] , zodat er een andere bestemming kan worden opgegeven.

U kunt para meters voor de webservice definiëren en deze koppelen aan een of meer module parameters, en u kunt opgeven of ze vereist of optioneel zijn. De gebruiker van de webservice biedt waarden voor deze para meters wanneer de service wordt geopend en de module acties dienovereenkomstig worden gewijzigd.

Zie [Using Azure machine learning web service para meters][webserviceparameters]voor meer informatie over de para meters van de web-service en hoe u deze kunt gebruiken.

In de volgende stappen wordt beschreven hoe u een voorspellend experiment implementeert als een nieuwe webservice. U kunt het experiment ook implementeren als klassieke webservice.

## <a name="deploy-it-as-a-new-web-service"></a>Deze als een nieuwe webservice implementeren

Nu het voorspellende experiment is voor bereid, kunt u dit implementeren als een nieuwe Azure-webservice (op basis van Resource Manager). Met de webservice kunnen gebruikers gegevens naar uw model verzenden en worden de voor spellingen van het model geretourneerd.

Als u uw voorspellende experiment wilt implementeren, klikt u op **uitvoeren** onder aan het canvas van het experiment. Zodra het experiment is voltooid, klikt u op **webservice implementeren** en selecteert u **Web service implementeren [Nieuw]**.  De pagina implementatie van de Web Service-Portal van Machine Learning Studio (klassiek) wordt geopend.

> [!NOTE] 
> Als u een nieuwe webservice wilt implementeren, moet u voldoende machtigingen hebben in het abonnement waarvoor u de webservice implementeert. Zie [Manage a web service using the Azure machine learning Web Services portal](manage-new-webservice.md)(Engelstalig) voor meer informatie. 

### <a name="web-service-portal-deploy-experiment-page"></a>Pagina Web Service Portal implementeren experiment

Voer op de pagina experiment implementeren een naam in voor de webservice.
Selecteer een prijs plan. Als u een bestaand prijs plan hebt, kunt u dit selecteren, anders moet u een nieuw prijs plan maken voor de service.

1. Selecteer een bestaand abonnement in de vervolg keuzelijst **prijs plan** of selecteer de optie **Nieuw abonnement selecteren** .
2. Typ in **naam van abonnement** een naam waarmee u het plan op uw factuur kunt identificeren.
3. Selecteer een van de **niveaus van de maandelijkse abonnementen**. Standaard worden de prijscategorieën voor uw standaardregio gebruikt en de webservice wordt ook in die regio geïmplementeerd.

Klik op **implementeren** en op de pagina **Quick** start voor uw webservice wordt geopend.

De pagina Quick Start van web service biedt u toegang tot en richt lijnen voor de meest voorkomende taken die u moet uitvoeren na het maken van een webservice. Hier kunt u eenvoudig toegang krijgen tot zowel de pagina test en verbruik.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Uw nieuwe webservice testen

Als u uw nieuwe webservice wilt testen, klikt u op **webservice testen** onder algemene taken. Op de pagina test kunt u uw webservice testen als een Request-Response service (RR'S) of een batch Execution Service (BES).

Op de pagina bron records testen worden de invoer, uitvoer en alle globale para meters weer gegeven die u voor het experiment hebt gedefinieerd. Als u de webservice wilt testen, kunt u hand matig de juiste waarden voor de invoer invoeren of een bestand met door komma's gescheiden waarden (CSV) met de test waarden opgeven.

Als u wilt testen met behulp van bron records, voert u in de lijst weergave modus de juiste waarden in voor de invoer en klikt u op **aanvraag-antwoord testen**. De resultaten van de voor spelling worden weer gegeven in de kolom uitvoer aan de linkerkant.

![Voer de juiste waarden in om de webservice te testen](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Als u uw BES wilt testen, klikt u op **batch**. Klik op de pagina batch test op Bladeren onder uw invoer en selecteer een CSV-bestand met de juiste voorbeeld waarden. Als u geen CSV-bestand hebt en u uw voorspellende experiment hebt gemaakt met behulp van Machine Learning Studio (klassiek), kunt u de gegevensset voor uw voorspellende experiment downloaden en gebruiken.

Als u de gegevensset wilt downloaden, opent u Machine Learning Studio (klassiek). Open uw voorspellende experiment en klik met de rechter muisknop op de invoer voor uw experiment. Selecteer in het context menu de optie **gegevensset** en selecteer vervolgens **downloaden**.

![Uw gegevensset downloaden van het Studio-canvas (klassiek)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klik op **testen**. De status van uw batch-uitvoerings taak wordt weer gegeven aan de rechter kant onder **batch-taken testen**.

![Uw batch-uitvoerings taak testen met de Web Service-Portal](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Op de pagina **configuratie** kunt u de beschrijving, de titel, de sleutel van het opslag account bijwerken en voorbeeld gegevens voor uw webservice inschakelen.

![Uw webservice configureren](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Toegang tot uw nieuwe webservice

Wanneer u de webservice hebt geïmplementeerd vanuit Machine Learning Studio (klassiek), kunt u gegevens verzenden naar de service en antwoorden ontvangen via een programma.

De pagina **verbruik** bevat alle informatie die u nodig hebt om toegang te krijgen tot uw webservice. De API-sleutel wordt bijvoorbeeld verschaft om geautoriseerde toegang tot de service toe te staan.

Zie [How to verbruik a Azure machine learning Studio (klassieke) Web Service (Engelstalig)](consume-web-services.md)voor meer informatie over het openen van een machine learning Studio (klassieke) webservice.

### <a name="manage-your-new-web-service"></a>Uw nieuwe webservice beheren

U kunt uw nieuwe webservices beheren met de Web Services-portal van Machine Learning Studio (Classic). Klik op de [hoofd pagina](https://services.azureml.net/)van de portal op **Web Services**. Op de pagina webservices kunt u een service verwijderen of kopiëren. Als u een specifieke service wilt bewaken, klikt u op de service en klikt u vervolgens op **dash board**. Als u batch-taken wilt bewaken die zijn gekoppeld aan de webservice, klikt u op **Batch-aanvraag logboek**.

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a> Uw nieuwe webservice implementeren in meerdere regio's

U kunt eenvoudig een nieuwe webservice implementeren in meerdere regio's zonder dat er meerdere abonnementen of werk ruimten nodig zijn.

Prijzen zijn specifiek voor regio's. u moet dus een facturerings plan definiëren voor elke regio waarin u de-webservice gaat implementeren.

#### <a name="create-a-plan-in-another-region"></a>Een plan maken in een andere regio

1. Meld u aan bij [Microsoft Azure machine learning-webservices](https://services.azureml.net/).
2. Klik op de menu optie **plannen** .
3. Klik op de pagina plannen over weer gave op **Nieuw**.
4. Selecteer in de vervolg keuzelijst **abonnement** het abonnement waarin het nieuwe plan wordt opgeslagen.
5. Selecteer in de vervolg keuzelijst **regio** een regio voor het nieuwe plan. De plannings opties voor de geselecteerde regio worden weer gegeven in de sectie **plan opties** van de pagina.
6. Selecteer een resource groep voor het plan uit de vervolg keuzelijst **resource groep** . Zie [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md)(Engelstalig) voor meer informatie over resource groepen.
7. Typ in **naam van plan** de naam van het plan.
8. Klik onder **Opties plannen** op het facturerings niveau voor het nieuwe abonnement.
9. Klik op **Create**.

#### <a name="deploy-the-web-service-to-another-region"></a>De webservice implementeren in een andere regio

1. Klik op de pagina webservices Microsoft Azure Machine Learning op de menu optie **webservices** .
2. Selecteer de webservice die u naar een nieuwe regio implementeert.
3. Klik op **kopiëren**.
4. In de naam van de **webservice** typt u een nieuwe naam voor de webservice.
5. Typ in **Web Service Description** een beschrijving voor de webservice.
6. Selecteer in de vervolg keuzelijst **abonnement** het abonnement waarin de nieuwe webservice wordt opgeslagen.
7. Selecteer een resource groep voor de webservice in de vervolg keuzelijst **resource groep** . Zie [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md)(Engelstalig) voor meer informatie over resource groepen.
8. Selecteer in de vervolg keuzelijst **regio** de regio waarin u de webservice wilt implementeren.
9. Selecteer een opslag account in de vervolg keuzelijst **opslag account** om de webservice op te slaan.
10. Selecteer in de vervolg keuzelijst **prijs plan** een abonnement in de regio die u in stap 8 hebt geselecteerd.
11. Klik op **kopiëren**.

## <a name="deploy-it-as-a-classic-web-service"></a>De service implementeren als een klassieke webservice

Nu het voorspellende experiment voldoende is voor bereid, kunt u dit implementeren als een klassieke Azure-webservice. Met de webservice kunnen gebruikers gegevens naar uw model verzenden en worden de voor spellingen van het model geretourneerd.

Als u uw voorspellende experiment wilt implementeren, klikt u op **uitvoeren** onder aan het canvas en klikt u vervolgens op **webservice implementeren**. De webservice wordt ingesteld en u wordt in het dash board van de webservice geplaatst.

![Uw webservice implementeren vanuit Studio (klassiek)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Uw klassieke webservice testen

U kunt de webservice testen in de Machine Learning Studio (klassieke) Web Services-portal of Machine Learning Studio (klassiek).

Als u de webservice aanvraag antwoord wilt testen, klikt u op de knop **testen** in het dash board van de webservice. Er verschijnt een dialoog venster waarin u wordt gevraagd om de invoer gegevens voor de service. Dit zijn de kolommen die worden verwacht door het Score-experiment. Voer een set gegevens in en klik vervolgens op **OK**. De resultaten die door de webservice worden gegenereerd, worden onder aan het dash board weer gegeven.

U kunt klikken op de koppeling **test** voorbeeld om uw service te testen in de Web Services-portal van Azure machine learning Studio (klassiek) zoals eerder in de sectie nieuwe webservice wordt weer gegeven.

Als u de service voor batch uitvoering wilt testen, klikt u op de koppeling **test** voorbeeld. Klik op de pagina batch test op Bladeren onder uw invoer en selecteer een CSV-bestand met de juiste voorbeeld waarden. Als u geen CSV-bestand hebt en u uw voorspellende experiment hebt gemaakt met behulp van Machine Learning Studio (klassiek), kunt u de gegevensset voor uw voorspellende experiment downloaden en gebruiken.

![De webservice testen](./media/publish-a-machine-learning-web-service/figure-3.png)

Op de pagina **configuratie** kunt u de weergave naam van de service wijzigen en een beschrijving geven. De naam en beschrijving worden weer gegeven in de [Azure Portal](https://portal.azure.com/) waar u uw webservices beheert.

U kunt een beschrijving opgeven voor de invoer gegevens, uitvoer gegevens en para meters van de webservice door een teken reeks op te geven voor elke kolom onder **invoer schema**, **uitvoer schema** en **Web Service-para meter**. Deze beschrijvingen worden gebruikt in de voorbeeld code documentatie van de webservice.

U kunt logboek registratie inschakelen om te controleren of er fouten zijn opgetreden die u ziet wanneer de webservice wordt geopend. Zie [logboek registratie inschakelen voor machine learning Studio (klassieke) webservices](web-services-logging.md)voor meer informatie.

![Logboek registratie inschakelen in de Web Services-portal](./media/publish-a-machine-learning-web-service/figure-4.png)

U kunt de eind punten voor de webservice ook configureren in de Azure Machine Learning Web Services-portal, vergelijkbaar met de procedure die eerder in de sectie nieuwe webservice wordt weer gegeven. De opties verschillen, u kunt de beschrijving van de service toevoegen of wijzigen, logboek registratie inschakelen en voorbeeld gegevens inschakelen voor testen.

### <a name="access-your-classic-web-service"></a>Toegang tot uw klassieke webservice

Wanneer u de webservice hebt geïmplementeerd vanuit Azure Machine Learning Studio (klassiek), kunt u gegevens verzenden naar de service en antwoorden ontvangen via een programma.

Het dash board bevat alle informatie die u nodig hebt om toegang te krijgen tot uw webservice. De API-sleutel wordt bijvoorbeeld verschaft om geautoriseerde toegang te verlenen tot de service, en API Help-pagina's worden weer gegeven om u te helpen uw code te schrijven.

Zie [How to verbruik a Azure machine learning Studio (klassieke) Web Service (Engelstalig)](consume-web-services.md)voor meer informatie over het openen van een machine learning Studio (klassieke) webservice.

### <a name="manage-your-classic-web-service"></a>Uw klassieke webservice beheren

Er zijn verschillende acties die u kunt uitvoeren om een webservice te bewaken. U kunt deze bijwerken en verwijderen. U kunt ook extra eind punten toevoegen aan een klassieke webservice naast het standaard eindpunt dat wordt gemaakt wanneer u het implementeert.

Voor meer informatie, Zie [een Azure machine learning Studio klassieke werk ruimte beheren](manage-workspace.md) en [een webservice beheren via de portal voor webservices Azure machine learning Studio (klassiek)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>De webservice bijwerken
U kunt wijzigingen aanbrengen in uw webservice, zoals het bijwerken van het model met aanvullende trainings gegevens en het opnieuw implementeren, waarbij de oorspronkelijke webservice wordt overschreven.

Als u de webservice wilt bijwerken, opent u het oorspronkelijke voorspellende experiment dat u hebt gebruikt om de webservice te implementeren en maakt u een Bewerk bare kopie door te klikken op **Opslaan als**. Breng uw wijzigingen aan en klik vervolgens op **webservice implementeren**.

Omdat u dit experiment eerder hebt geïmplementeerd, wordt u gevraagd of u de bestaande service wilt overschrijven (klassieke webservice) of bijwerken (nieuwe webservice). Als u op **Ja** of **bijwerken** klikt, wordt de bestaande webservice gestopt en wordt het nieuwe voorspellende experiment geïmplementeerd.

> [!NOTE]
> Als u wijzigingen in de configuratie hebt aangebracht in de oorspronkelijke webservice, bijvoorbeeld door een nieuwe weergave naam of beschrijving in te voeren, moet u deze waarden opnieuw invoeren.

Een optie voor het bijwerken van uw webservice is het model via een programma opnieuw te trainen. Zie voor meer informatie [(Engelstalig) Retrain machine learning Studio-modellen (klassiek) via een programma](./retrain-machine-learning-model.md).

## <a name="next-steps"></a>Volgende stappen

* Zie [How a machine learning Studio (klassiek) model (Engelstalig)](model-progression-experiment-to-web-service.md)voor meer technische informatie over de werking van de implementatie van een experiment naar een operationele webservice.

* Zie voor meer informatie over het voorbereiden van uw model voor het implementeren van uw model [voor implementatie in azure machine learning Studio (klassiek)](deploy-a-machine-learning-web-service.md).

* Er zijn verschillende manieren om de REST-API te gebruiken en toegang te krijgen tot de webservice. Zie [een Azure machine learning Studio-webservice (klassiek) gebruiken](consume-web-services.md).

<!-- internal links -->
[Een trainings experiment maken]: #create-a-training-experiment
[Converteren naar een voorspellend experiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Nieuwe webservice]: #deploy-it-as-a-new-web-service
[Klassieke webservice]: #deploy-it-as-a-classic-web-service
[Nieuw]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[export-data]: /azure/machine-learning/studio-module-reference/export-data
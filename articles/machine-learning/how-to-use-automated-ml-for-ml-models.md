---
title: AutoML gebruiken om modellen te maken & implementeren
titleSuffix: Azure Machine Learning
description: Maak, Bekijk en implementeer automatische machine learning modellen met de Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: nibaccam
author: aniththa
ms.reviewer: nibaccam
ms.date: 12/20/2020
ms.topic: conceptual
ms.custom: how-to, automl
ms.openlocfilehash: 2e06375441d6540d6630cfe9d4d8c3beec558879
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562719"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Automatische machine learning modellen maken, controleren en implementeren met Azure Machine Learning


In dit artikel leert u hoe u geautomatiseerde machine learning modellen kunt maken, verkennen en implementeren zonder één regel code in Azure Machine Learning Studio.

Automatische machine learning is een proces waarbij het beste machine learning algoritme voor uw specifieke gegevens wordt geselecteerd. Met dit proces kunt u snel machine learning modellen genereren. Meer [informatie over automatische machine learning](concept-automated-ml.md).
 
Voor een end-to-end-voor beeld probeert [u de zelf studie voor het maken van een classificatie model met de automatische ml-interface van Azure machine learning](tutorial-first-experiment-automated-ml.md). 

[Configureer uw geautomatiseerde machine learning experimenten](how-to-configure-auto-train.md) met de Azure machine learning SDK voor een op een python-code gebaseerde ervaring.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Een Azure Machine Learning-werkruimte. Raadpleeg [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md). 

## <a name="get-started"></a>Aan de slag

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com). 

1. Selecteer uw abonnement en werk ruimte. 

1. Ga naar het linkerdeel venster. Selecteer **automatische ml** onder het gedeelte **Auteur** .

[![Navigatievenster van Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Als dit de eerste keer is dat er experimenten worden uitgevoerd, ziet u een lege lijst en koppelingen naar documentatie. 

Als dat niet het geval is, ziet u een lijst met uw recente geautomatiseerde machine learning experimenten, met inbegrip van die zijn gemaakt met de SDK. 

## <a name="create-and-run-experiment"></a>Experiment maken en uitvoeren

1. Selecteer **+ nieuwe automatische ml run** en vul het formulier in.

1. Selecteer een gegevensset uit uw opslag container of maak een nieuwe gegevensset. Gegevens sets kunnen worden gemaakt op basis van lokale bestanden, Web-url's, gegevens opslag of Azure open gegevens sets. Meer informatie over het maken van een [gegevensset](how-to-create-register-datasets.md).  

    >[!Important]
    > Vereisten voor trainingsgegevens:
    >* Gegevens moeten in tabel vorm zijn.
    >* De waarde die u wilt voors pellen (doel kolom), moet aanwezig zijn in de gegevens.

    1. Als u een nieuwe gegevensset wilt maken op basis van een bestand op uw lokale computer, selecteert u **+ gegevensset maken** en selecteert u vervolgens **uit lokaal bestand**. 

    1. Geef in het formulier **basis informatie** uw gegevensset een unieke naam en geef een optionele beschrijving op. 

    1. Selecteer **volgende** om het **formulier gegevens opslag en bestand selecteren** te openen. Op dit formulier selecteert u waar u uw gegevensset wilt uploaden. de standaard opslag container die automatisch wordt gemaakt met uw werk ruimte, of kies een opslag container die u wilt gebruiken voor het experiment. 
    
        1. Als uw gegevens zich achter een virtueel netwerk bevinden, moet u de functie voor **het overs Laan** van de validatie inschakelen om ervoor te zorgen dat de werk ruimte toegang heeft tot uw gegevens. Zie [Azure machine learning Studio gebruiken in een virtueel netwerk van Azure](how-to-enable-studio-virtual-network.md)voor meer informatie. 
    
    1. Selecteer **Bladeren** om het gegevens bestand voor uw gegevensset te uploaden. 

    1. Controleer de **instellingen en het voorbeeld** formulier op nauw keurigheid. Het formulier wordt op intelligente wijze ingevuld op basis van het bestands type. 

        Veld| Beschrijving
        ----|----
        Bestandsindeling| Definieert de indeling en het type gegevens dat is opgeslagen in een bestand.
        Scheidingsteken| Een of meer tekens die de grens aangeven tussen  afzonderlijke, onafhankelijke regio's in tekst zonder opmaak of andere gegevensstromen.
        Encoding| Identificeert welke bit-naar-tekenschematabel er moet gebruikt worden om uw gegevensset te lezen.
        Kolomkoppen| Geeft aan hoe eventuele koppen van de gegevensset worden behandeld.
        Rijen overslaan | Geeft aan hoeveel rijen er eventueel worden overgeslagen in de gegevensset.
    
        Selecteer **Next**.

    1. Het **schema** formulier wordt op de slimme wijze ingevuld op basis van de selecties in het formulier **instellingen en preview** . Hier configureert u het gegevens type voor elke kolom, bekijkt u de kolom namen en selecteert u welke kolommen niet voor uw experiment moeten worden **toegevoegd** . 
            
        Selecteer **Volgende**.

    1. Het formulier **Details bevestigen** is een samen vatting van de gegevens die eerder zijn ingevuld in de **basis gegevens** en- **instellingen en preview** -formulieren. U kunt ook een gegevens profiel maken voor uw gegevensset met behulp van een profilerings functie ingeschakeld. Meer informatie over [gegevensprofilering](how-to-connect-data-ui.md#profile).

        Selecteer **Next**.
1. Selecteer de zojuist gemaakte gegevensset zodra deze wordt weer gegeven. U kunt ook een preview van de gegevensset en voorbeeld statistieken bekijken. 

1. Selecteer op het formulier **Run configureren** de optie **nieuwe maken** en voer **zelf studie-automl-Deploy** in voor de naam van het experiment.

1. Selecteer een doel kolom. Dit is de kolom waarop u de voor spellingen wilt uitvoeren.

1. Selecteer een compute voor de taak voor gegevens profilering en training. In de vervolg keuzelijst vindt u een lijst met uw bestaande berekeningen. Volg de instructies in stap 7 om een nieuwe Compute te maken.

1. Selecteer **een nieuwe Compute maken** om uw berekenings context voor dit experiment te configureren.

    Veld|Beschrijving
    ---|---
    Naam berekening| Voer een unieke naam in die uw berekenings context identificeert.
    Prioriteit van virtuele machine| Virtuele machines met lage prioriteit zijn goed koper, maar garanderen niet de reken knooppunten. 
    Type virtuele machine| Selecteer CPU of GPU voor type virtuele machine.
    Grootte van de virtuele machine| Selecteer de grootte van de virtuele machine voor uw berekening.
    Min / Max knooppunten| U moet u één of meer knooppunten opgeven om gegevens te profileren. Voer het maximum aantal knoop punten in voor de reken kracht. De standaard waarde is 6 knoop punten voor een AML-berekening.
    Geavanceerde instellingen | Met deze instellingen kunt u een gebruikers account en een bestaand virtueel netwerk configureren voor uw experiment. 
    
    Selecteer **Maken**. Het maken van een nieuwe berekening kan enkele minuten duren.

    >[!NOTE]
    > De naam van de berekening geeft aan of de compute die u selecteert/maakt, *profile ring is ingeschakeld*. (Zie de sectie [gegevens profilering](how-to-connect-data-ui.md#profile) voor meer informatie).

    Selecteer **Next**.

1. Selecteer op het **taak type en het instellingen** formulier het taak type: classificatie, regressie of prognose. Zie [ondersteunde taak typen](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) voor meer informatie.

    1. Voor **classificatie** kunt u ook diep gaande lessen inschakelen.
    
        Als grondige kennis is ingeschakeld, is validatie beperkt tot _train_validation splitsen_. Meer [informatie over validatie opties](how-to-configure-cross-validation-data-splits.md).


    1. Voor een **prognose** kunt u 
    
        1. Uitgebreide training inschakelen.
    
        1. Selecteer een *tijd kolom*: deze kolom bevat de tijd gegevens die moeten worden gebruikt.

        1. *Prognose horizon* selecteren: Geef aan hoeveel tijds eenheden (minuten/uren/dagen/weken/maanden/jaar) het model op de toekomst kan voors pellen. Verder is het model vereist om in de toekomst te voors pellen, hoe minder nauw keurig wordt. Meer [informatie over prognoses en prognoses horizon](how-to-auto-train-forecast.md).

1. Beschrijving Aanvullende configuratie-instellingen weer geven: extra instellingen die u kunt gebruiken om de trainings taak beter te beheren. Anders worden de standaardinstellingen toegepast op basis van de selectie en gegevens van het experiment. 

    Aanvullende configuraties|Beschrijving
    ------|------
    Primaire metrische gegevens| De belangrijkste waarde die wordt gebruikt voor het scoren van uw model. Meer [informatie over de metrische gegevens van modellen](how-to-configure-auto-train.md#primary-metric).
    Uitleg geven over het beste model | Selecteer deze optie om in of uit te scha kelen om uitleg voor het aanbevolen model weer te geven. <br> Deze functionaliteit is momenteel niet beschikbaar voor [bepaalde prognose algoritmen](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model). 
    Geblokkeerd algoritme| Selecteer de algoritmen die u wilt uitsluiten van de trainings taak. <br><br> Het toestaan van algoritmen is alleen beschikbaar voor [SDK-experimenten](how-to-configure-auto-train.md#supported-models). <br> Zie de [ondersteunde modellen voor elk taak type](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels).
    Criterium voor afsluiten| Wanneer aan een van deze criteria wordt voldaan, wordt de trainings taak gestopt. <br> *Tijd van trainings taak (uren)*: hoe lang het mogelijk is om de trainings taak uit te voeren. <br> *Drempel waarde voor metrische Score*: minimale metrische score voor alle pijp lijnen. Dit zorgt ervoor dat als u een gedefinieerde doel metriek hebt die u wilt bereiken, u niet meer tijd op de trainings taak brengt dan nodig is.
    Validatie| Selecteer een van de opties voor kruis validatie die u wilt gebruiken in de trainings taak. <br> Meer [informatie over Kruis validatie](how-to-configure-cross-validation-data-splits.md#prerequisites).<br> <br>Prognoses bieden alleen ondersteuning voor kruis validatie met k-vouwen.
    Gelijktijdigheid| Maximum aantal *gelijktijdige herhalingen*: Maxi maal toegestane pijp lijnen (iteraties) om in de trainings taak te testen. De taak wordt niet meer uitgevoerd dan het opgegeven aantal iteraties. Meer informatie over hoe automatische ML [meerdere onderliggende uitvoeringen op clusters](how-to-configure-auto-train.md#multiple-child-runs-on-clusters)uitvoert.

1. Beschrijving Parametrisatie-instellingen weer geven: als u ervoor kiest **automatische parametrisatie** in te scha kelen in het formulier **aanvullende configuratie-instellingen** , worden de standaard parametrisatie-technieken toegepast. In de **instellingen van de weer gave-parametrisatie** kunt u deze standaard waarden wijzigen en dienovereenkomstig aanpassen. Meer informatie over het [aanpassen van featurizations](#customize-featurization). 

    ![Scherm afbeelding toont het dialoog venster taak type selecteren met de instellingen voor weer gave-parametrisatie.](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

## <a name="customize-featurization"></a>Parametrisatie aanpassen

In het formulier **parametrisatie** kunt u automatische parametrisatie in-en uitschakelen en de automatische parametrisatie-instellingen voor uw experiment aanpassen. Als u dit formulier wilt openen, raadpleegt u stap 10 in het gedeelte [experiment maken en uitvoeren](#create-and-run-experiment) . 

De volgende tabel bevat een overzicht van de aanpassingen die momenteel beschikbaar zijn via Studio. 

Kolom| Aanpassing
---|---
Inbegrepen | Hiermee geeft u op welke kolommen moeten worden toegevoegd voor de training.
Onderdeel type| Wijzig het waardetype voor de geselecteerde kolom.
Toegerekend met| Selecteer welke waarde er ontbrekende waarden moeten worden toegerekend met in uw gegevens.

![Aangepaste parametrisatie voor Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Experiment uitvoeren en resultaten weer geven

Selecteer **volt ooien** om uw experiment uit te voeren. Het voorbereiden van het experiment kan tot 10 minuten duren. Trainingstaken kunnen nog 2-3 minuten meer kosten voordat het uitvoeren van elke pijplijn is voltooid.

> [!NOTE]
> De algoritmen die automatisch worden geautomatiseerd, hebben een inherente wille keurigheid waardoor kleine afwijkingen in de Score van de uiteindelijke metrische gegevens van een aanbevolen model, zoals nauw keurigheid, kunnen ontstaan. Automatische ML voert ook bewerkingen uit op gegevens zoals de splitsing van Train-test, gesplitste validatie of Kruis validatie wanneer dat nodig is. Als u dus een experiment met dezelfde configuratie-instellingen en primaire gegevens meerdere keren uitvoert, zult u waarschijnlijk de variatie van elke eindmetrieke meet waarde van elke experimenten zien als gevolg van deze factoren. 

### <a name="view-experiment-details"></a>Details van experiment weer geven

Het scherm **detail uitvoeren** wordt geopend op het tabblad **Details** . In dit scherm ziet u een overzicht van de uitvoering van het experiment, met inbegrip van een status balk bovenaan naast het uitvoerings nummer. 

Het tabblad **Modellen** bevat een lijst met de gemaakte modellen, op volgorde van de metrische score. Standaardstaat het model dat het hoogst scoort op basis van het gekozen metrische gegeven bovenaan de lijst. Terwijl de trainingstaak meer modellen uitprobeert, worden deze toegevoegd aan de lijst. Gebruik dit om een snelle vergelijking te krijgen van de metrische gegevens voor de tot dusver geproduceerde modellen.

![Uitvoeringsdetails](./media/how-to-use-automated-ml-for-ml-models/explore-models.gif)

### <a name="view-training-run-details"></a>Details van de cursus uitvoering weer geven

Inzoomen op een van de voltooide modellen om details van de trainings uitvoering te bekijken, zoals een model samenvatting op het tabblad **model** of de grafieken met metrische gegevens over prestaties op het tabblad **metrische gegevens** . meer [informatie over grafieken](how-to-understand-automated-ml.md).

[![Details van herhaling](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="model-explanations"></a>Model uitleg

Als u meer inzicht wilt krijgen in uw model, kunt u zien welke gegevens functies (onbewerkt of speciaal) invloed hebben op de voor spellingen van het model met het dash board voor model verklaringen. 

Het dash board voor het model uitleg bevat een algemene analyse van het getrainde model samen met de voor spellingen en toelichtingen. Daarnaast kunt u hiermee inzoomen op een afzonderlijk gegevens punt en de afzonderlijke functie-belang rijkheid. Meer [informatie over de visualisaties en specifieke waarnemings punten van het uitleg-dash board](how-to-machine-learning-interpretability-aml.md#visualizations).

Om uitleg voor een bepaald model op te halen, 

1. Selecteer op het tabblad **modellen** het model dat u wilt gebruiken. 
1. Selecteer de knop **model uitleggen** en geef een berekening op die kan worden gebruikt om de uitleg te genereren.
1. Controleer het tabblad voor **onderliggende uitvoeringen** voor de status. 
1. Zodra het proces is voltooid, gaat u naar het tabblad **uitleg (preview)** dat het dash board uitleg bevat. 

    ![Model uitleg van het dash board](media/how-to-use-automated-ml-for-ml-models/model-explanation-dashboard.png)

## <a name="deploy-your-model"></a>Uw model implementeren

Zodra u het beste model hebt gevonden, is het tijd om dit te implementeren als een webservice, om nieuwe gegevens te voorspellen.

>[!TIP]
> Als u een model wilt implementeren dat via het `automl` pakket met de python-SDK is gegenereerd, moet u [uw model registreren](how-to-deploy-and-where.md?tabs=python#register-a-model-from-an-azure-ml-training-run-1) bij de werk ruimte. 
>
> Zodra u het model hebt geregistreerd, kunt u het vinden in de Studio door **modellen** te selecteren in het linkerdeel venster. Zodra u het model hebt geopend, kunt u de knop **implementeren** selecteren boven aan het scherm en de instructies volgen zoals beschreven in **stap 2** van de sectie **uw model implementeren** .

Geautomatiseerde ML helpt u bij het implementeren van het model zonder code te schrijven:

1. U hebt een aantal opties voor implementatie. 

    + Optie 1: Implementeer het beste model volgens de criteria die u hebt gedefinieerd. 
        1. Nadat het experiment is voltooid, gaat u naar de bovenliggende uitvoerings pagina door **uitvoeren 1** aan de bovenkant van het scherm te selecteren. 
        1.  Selecteer het model dat wordt weer gegeven in de sectie **Best samen vatting** van het model. 
        1. Selecteer **implementeren** in de linkerbovenhoek van het venster. 

    + Optie 2: een specifieke model herhaling uit dit experiment implementeren.
        1. Selecteer het gewenste model op het tabblad **Modellen**
        1. Selecteer **implementeren** in de linkerbovenhoek van het venster.

1. Vul het deel venster **model implementeren** in.

    Veld| Waarde
    ----|----
    Naam| Voer een unieke naam in voor uw implementatie.
    Beschrijving| Voer een beschrijving in om beter te kunnen identificeren waarvoor deze implementatie is.
    Rekentype| Selecteer het type eind punt dat u wilt implementeren: *Azure Kubernetes service (AKS)* of *Azure container instance (ACI)*.
    Naam berekening| *Is alleen van toepassing op AKS:* Selecteer de naam van het AKS-cluster waarnaar u wilt implementeren.
    Verificatie inschakelen | Selecteer deze optie om verificatie op basis van tokens of sleutel toe te staan.
    Aangepaste implementatie-assets gebruiken| Schakel deze functie in als u uw eigen score script en omgevings bestand wilt uploaden. [Meer informatie over scorescripts](how-to-deploy-and-where.md).

    >[!Important]
    > Bestands namen moeten minder dan 32 tekens lang zijn en moeten beginnen en eindigen met een alfanumerieke teken reeks. De rest van de naam mag streepjes, onderstrepingstekens, punten en alfanumerieke tekens bevatten. Spaties zijn niet toegestaan.

    Het menu *Geavanceerd* biedt standaard implementatiefuncties, zoals [gegevensverzameling](how-to-enable-app-insights.md), en instellingen voor het gebruik van resources. Als u deze standaardwaarden wilt overschrijven, kunt u dit doen in dit menu.

1. Selecteer **Implementeren**. Implementatie duurt ongeveer 20 minuten.
    Zodra de implementatie is gestart, wordt het tabblad **Overzicht van model** weergegeven. U kunt de voortgang van de implementatie bekijken in de sectie **Implementatiestatus**. 

U hebt nu een operationele webservice om voorspellingen te genereren. U kunt de voorspellingen testen door de service te doorzoeken via [Ondersteuning voor ingebouwde Azure Machine Learning van Power BI](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context).

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het gebruik van een webservice](how-to-consume-web-service.md).
* Krijg [inzicht in geautomatiseerde machine learning resultaten](how-to-understand-automated-ml.md).
* Meer [informatie over automatische machine learning](concept-automated-ml.md) en Azure machine learning.
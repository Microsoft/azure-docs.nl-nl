---
title: 'Zelfstudie: Vraagprognose en AutoML'
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen en implementeren van een vraagprognosemodel met geautomatiseerde machine learning in Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: 2653161b5828d89858234a9ca98fe432e0eacb5c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879357"
---
# <a name="tutorial-forecast-demand-with-automated-machine-learning"></a>Zelfstudie: Vraag voorspellen met automatische machine learning


In deze zelfstudie gebruikt u geautomatiseerde machine learning, of geautomatiseerde ML, in de Azure Machine Learning Studio om een prognosemodel voor tijdreeksen te maken om de huurvraag voor een service voor het delen van fietsen te voorspellen.

Voor een voorbeeld van een classificatiemodel, zie [zelfstudie: Een classificatiemodel maken met geautomatiseerde ML in Azure Machine Learning](tutorial-first-experiment-automated-ml.md).

In deze zelfstudie leert u hoe u de volgende taken uitvoert:

> [!div class="checklist"]
> * Een gegevensset maken en laden.
> * Een automatisch ML-experiment configureren en uitvoeren.
> * Prognose-instellingen specificeren.
> * De resultaten van het experiment verkennen.
> * Het beste model implementeren.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Raadpleeg [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md). 

* Het gegevensbestand [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) downloaden

## <a name="get-started-in-azure-machine-learning-studio"></a>Aan de slag met Azure Machine Learning Studio

Voor deze zelfstudie maakt u een geautomatiseerd ML-experiment in Azure Machine Learning Studio, een geconsolideerde webinterface met hulpmiddelen voor machine learning waar gegevenswetenschappers, ongeacht hun vaardigheidsniveaus, scenario's kunnen uitvoeren. De Studio wordt niet ondersteund in Internet Explorer-browsers.

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com).

1. Selecteer uw abonnement en de werkruimte die u hebt gecreëerd.

1. Selecteer **Aan de slag**.

1. Selecteer in het linkerdeelvenster **Geautomatiseerde ML** in de sectie **Maken**.

1. Selecteer **Nieuwe geautomatiseerde ML-uitvoering**. 

## <a name="create-and-load-dataset"></a>Gegevensset maken en laden

Voordat u uw experiment gaat configureren, uploadt u uw gegevensbestand naar uw werkruimte in de vorm van een Azure Machine Learning-gegevensset. Als u dit doet, kunt u ervoor zorgen dat uw gegevens op de juiste wijze zijn opgemaakt voor uw experiment.

1. Op het formulier **Gegevensset selecteren**, selecteert u **Uit lokale bestanden** in de keuzelijst **+Gegevensset maken**. 

    1. Geef uw gegevensset een naam en een optionele beschrijving in het formulier **Basisinformatie**. Het type gegevensset moet standaard in **Tabelvorm** zijn, omdat automatische ML in Azure Machine Learning Studio momenteel alleen ondersteuning biedt voor gegevenssets in tabelvorm.
    
    1. Selecteer **Volgende** in de linkerbenedenhoek

    1. Selecteer in het formulier **Gegevensarchief- en bestandsselectie** het standaard gegevensarchief dat automatisch is ingesteld bij het maken van uw werkruimte, **workspaceblobstore (Azure Blob Storage)** . Dit is de opslaglocatie waar u uw gegevensbestand uploadt. 

    1. Selecteer **Bladeren**. 
    
    1. Kies het bestand **bike-no.csv** op uw lokale computer. Dit is het bestand dat u hebt gedownload als [vereiste](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv).

    1. Selecteer **Volgende**

       Wanneer het uploaden is voltooid, worden de instellingen en het voorbeeldformulier automatisch ingevuld op basis van het bestandstype. 
       
    1. Controleer of het formulier **Instellingen en voorbeeld** als volgt is ingevuld en selecteer **Volgende**.
        
        Veld|Beschrijving| Waarde voor zelfstudie
        ---|---|---
        Bestandsindeling|Definieert de indeling en het type gegevens dat is opgeslagen in een bestand.| Met scheidingstekens
        Scheidingsteken|Een of meer tekens die de grens aangeven tussen &nbsp; afzonderlijke, onafhankelijke regio's in tekst zonder opmaak of andere gegevensstromen. |Komma
        Encoding|Identificeert welke bit-naar-tekenschematabel er moet gebruikt worden om uw gegevensset te lezen.| UTF-8
        Kolomkoppen| Geeft aan hoe koppen van de gegevensset eventueel worden behandeld.| Kopteksten uit het eerste bestand gebruiken
        Rijen overslaan | Geeft aan hoeveel rijen er eventueel worden overgeslagen in de gegevensset.| Geen

    1. Met het formulier **Schema** kunt u uw gegevens verder configureren voor dit experiment. 
    
        1. Voor dit voorbeeld negeert u de kolommen **Informeel** en **Geregistreerd**. Deze kolommen vormen een uitsplitsing van de kolom **cnt**, dus we nemen deze niet op.

        1. Voor dit voorbeeld moet u ook de standaardwaarden voor de **Eigenschappen** en **Type** opgeven. 
        
        1. Selecteer **Next**.

    1. Controleer of de informatie in het formulier **Details bevestigen** overeenkomt met wat voorheen in de formulieren **Basisinformatie** en **Instellingen en voorbeeld** is ingevuld.

    1. Selecteren **Maken** om uw gegevensset te voltooien.

    1. Selecteer uw gegevensset wanneer deze verschijnt in de lijst.

    1. Selecteer **Volgende**.

## <a name="configure-run"></a>Uitvoering configureren

Nadat u uw gegevens hebt geladen en geconfigureerd, stelt u uw externe rekendoel in en selecteert u welke kolom in uw gegevens u wilt voorspellen.

1. Vul het formulier **Uitvoering configureren** als volgt in:
    1. Een naam voor het experiment invoeren: `automl-bikeshare`

    1. Selecteer **cnt** als doelkolom, wat u wilt voorspellen. In deze kolom wordt het totale aantal gehuurde fietsen van bikeshare aangegeven.

    1. Selecteer **Een nieuwe berekening maken** en configureer uw rekendoel. Automatische ML ondersteunt alleen Azure Machine Learning-berekeningen. 

        1. Vul het formulier **Virtuele machine** in om de berekening in te stellen.

            Veld | Beschrijving | Waarde voor zelfstudie
            ----|---|---
            Prioriteit van virtuele&nbsp;machine&nbsp; |Selecteer de prioriteit die het experiment moet krijgen| Toegewezen
            Virtuele&nbsp;machine&nbsp;type| Selecteer het type van de virtuele machine voor uw berekening.|CPU (Central Processing Unit, centrale verwerkingseenheid)
            Grootte&nbsp;virtuele&nbsp;machine| Selecteer de grootte van de virtuele machine voor uw berekening. Er wordt een lijst met aanbevolen grootten geboden, op basis van uw gegevens en het type experiment. |Standard_DS12_V2
        
        1. Selecteer **Volgende** om het **formulier Instellingen configureren** in te vullen.
        
             Veld | Beschrijving | Waarde voor zelfstudie
            ----|---|---
            Naam berekening |  Een unieke naam die de context van uw berekening identificeert. | bike-compute
            Min / Max knooppunten| U moet u één of meer knooppunten opgeven om gegevens te profileren.|Min. knooppunten: 1<br>Max. knooppunten: 6
            Seconden wachten voor omlaag schalen | Niet-actieve tijd voordat het cluster automatisch omlaag wordt geschaald naar het minimum aantal knooppunten.|120 (standaardinstelling)
            Geavanceerde instellingen | Instellingen voor het configureren en autoriseren van een virtueel netwerk voor uw experiment.| Geen 
  
        1. Selecteer **Maken** om het rekendoel op te halen. 

            **Dit duurt enkele minuten.** 

        1. Wanneer dit is voltooid, selecteert u uw nieuwe rekendoel uit de vervolgkeuzelijst.

    1. Selecteer **Next**.

## <a name="select-forecast-settings"></a>Prognose-instellingen selecteren

Voltooi de installatie voor uw automatische ML-experiment door het taaktype en de configuratie-instellingen van de machine learning op te geven.

1. Selecteer op het formulier **Taaktype en instellingen** de optie **Prognose tijdreeks** als het type machine learning-taak.

1. Selecteer **datum** als uw **Tijdkolom** en laat **Tijdreeks-id’s** leeg. 

1. De **prognoseperiode** is hoe ver in de toekomst u voorspellingen wilt maken.  Hef de selectie automatische detectie op en typ 14 in het veld. 

1. Selecteer **Aanvullende configuratie-instellingen weergeven** en vul de velden als volgt in. Deze instellingen zijn bedoeld om de trainingstaak beter te besturen en om instellingen voor uw prognose op te geven. Anders worden de standaardinstellingen toegepast op basis van de selectie en gegevens van het experiment.

    Aanvullende&nbsp;configuraties|Beschrijving|Waarde&nbsp;voor&nbsp;zelfstudie
    ------|---------|---
    Primaire metrische gegevens| Evaluatiewaarde waarmee het machine learning-algoritme wordt gemeten.|Genormaliseerde wortel gemiddelde kwadraatfout
    Uitleg geven over het beste model| Hiermee wordt automatisch uitleg gegeven over het beste model dat is gemaakt met geautomatiseerde ML.| Inschakelen
    Geblokkeerde algoritmen | Algoritmen die u niet wilt opnemen in de trainingstaak| Extreme willekeurige structuren
    Aanvullende prognose-instellingen| Deze instellingen helpen de nauwkeurigheid van het model te verbeteren. <br><br> _**Doelvertragingen voor prognose:**_ hoe ver terug u de vertragingen van de doelvariabele wilt maken <br> _**Doorlopend doel**_: hiermee geeft u de grootte van het doorlopende venster op waarover de functies, zoals de *Max, min* en *Som*, worden gegenereerd. | <br><br>Doelvertragingen &nbsp;voor&nbsp;prognose: Geen <br> Formaat&nbsp;doorlopende &nbsp;doelgrootte&nbsp;: Geen
    Criterium voor afsluiten| Als er aan een criterium is voldaan, wordt de trainingstaak gestopt. |Tijd voor&nbsp;trainingstaak&nbsp; (uur): 3 <br> Drempelwaarde&nbsp;metrische&nbsp;score: Geen
    Validatie | Kies een kruisvalidatietype en een aantal tests.|Validatietype:<br>&nbsp;k-voudige&nbsp;kruisvalidatie <br> <br> Aantal validaties: 5
    Gelijktijdigheid| Het maximum aantal parallelle iteraties uitgevoerd per iteratie| Maximumaantal&nbsp;gelijktijdige&nbsp;iteraties: 6
    
    Selecteer **Opslaan**.

## <a name="run-experiment"></a>Experiment uitvoeren

Selecteer **Voltooien** om uw experiment uit te voeren. Het scherm **Uitvoergegevens** wordt geopend met de **Uitvoerstatus** bovenaan naast het uitvoernummer. Deze status wordt bijgewerkt wanneer het experiment wordt uitgevoerd. Meldingen worden ook weergegeven in de rechterbovenhoek van Studio, zodat u op de hoogte blijft van de status van het experiment.

>[!IMPORTANT]
> Het duurt **10-15 minuten** om de experimentele uitvoerbewerking voor te bereiden.
> Zodra de uitvoering is gestart duurt het **2-3 minuten langer per iteratie**.<br> <br>
> In productie zou u waarschijnlijk even weglopen omdat dit proces tijd in beslag neemt. Terwijl u wacht, wordt u aangeraden de geteste algoritmen te verkennen op het tabblad **Modellen**. 

##  <a name="explore-models"></a>Modellen bekijken

Ga naar het tabblad **Modellen** om de geteste algoritmen (modellen) te bekijken. De modellen worden standaard gerangschikt op hun metrische score terwijl ze worden voltooid. Voor deze zelfstudie staat het model dat het hoogst scoort op basis van de gekozen metrische gegevens **Genormaliseerde wortel gemiddelde kwadraatfout** bovenaan de lijst.

Terwijl u wacht tot alle experimentmodellen zijn voltooid, kunt u de **Algoritmenaam** van een volledig model selecteren om de prestatiedetails te bekijken. 

In het volgende voorbeeld kunt u naar de tabbladen **Details** en **Metrische gegevens** gaan om de eigenschappen, metrische gegevens en prestatiegrafieken van het geselecteerde model te bekijken. 

![Uitvoeringsdetails](./media/tutorial-automated-ml-forecast/explore-models.gif)

## <a name="deploy-the-model"></a>Het model implementeren

Met geautomatiseerde machine learning in Azure Machine Learning Studio kunt u met enkele stappen het beste model implementeren als webservice. Implementatie is de integratie van het model zodat het nieuwe gegevens kan voorspellen en potentiële kansgebieden kan identificeren. 

Voor dit experiment houdt het implementeren naar een webservice in dat het BikeShare-bedrijf nu een iteratieve en schaalbare weboplossing heeft voor het voorspellen van de vraag naar het huren van fietsen. 

Zodra de uitvoering is voltooid, gaat u terug naar bovenliggende uitvoeringspagina door **1 uitvoeren** aan de bovenkant van het scherm te selecteren.

In de sectie **Beste modeloverzicht** wordt **StackEnsemble** beschouwd in context van dit experiment, op basis van de metrische gegevens **genormaliseerde wortel gemiddelde kwadraatfout**.  

We implementeren dit model, maar houd er rekening mee dat implementatie ongeveer 20 minuten duurt. Het implementatieproces omvat verschillende stappen, waaronder het model registreren, resources genereren en ze configureren voor de webservice.

1. Selecteer **StackEnsemble** om de model-specifieke pagina te openen.

1. Selecteer de knop **Implementeren** in het gedeelte linksboven in het scherm.

1. Vul het deelvenster **Een model implementeren** als volgt in:

    Veld| Waarde
    ----|----
    Naam van implementatie| Implementeren Bike share
    Beschrijving van implementatie| implementatie van vraag naar bike share
    Rekentype | Azure Compute Instance (ACI) selecteren
    Verificatie inschakelen| Uitgeschakeld. 
    Aangepaste implementatie-assets gebruiken| Uitgeschakeld. Met Uitschakelen staat u toe dat het standaard stuurprogrammabestand (scorescript) en het omgevingsbestand automatisch worden gegenereerd. 
    
    In dit voorbeeld gebruiken we de standaardwaarden in het menu *Geavanceerd*. 

1. Selecteer **Implementeren**.  

    Bovenaan het scherm **Uitvoeren** wordt een groen bericht weergegeven dat de implementatie is gestart. De voortgang van de implementatie kan u vinden in het deelvenster **Modeloverzicht** onder **Status implementen**.
    
Zodra de implementatie is voltooid, hebt u een operationele webservice om voorspellingen te genereren. 

Ga verder met de [**Volgende stappen**](#next-steps) voor meer informatie over het gebruik van uw nieuwe webservice en test uw voorspellingen met de ingebouwde ondersteuning voor Azure Machine Learning van Power BI.

## <a name="clean-up-resources"></a>Resources opschonen

Implementatiebestanden zijn groter dan gegevens- en experimentbestanden. Daarom kost het meer om ze op te slaan. Verwijder alleen de implementatiebestanden om de kosten voor uw account te beperken, of als u uw werkruimte en experimentbestanden wilt behouden. Zo niet, verwijder dan de volledige resourcegroep als u geen enkel bestand wilt gebruiken.  

### <a name="delete-the-deployment-instance"></a>Het implementatie-exemplaar verwijderen

Verwijder alleen het implementatie-exemplaar van Azure Machine Learning indien u de resourcegroep en werkruimte wilt behouden voor andere zelfstudies en verkenning. 

1. Ga naar de [Azure Machine Learning Studio](https://ml.azure.com/). Ga naar uw werkruimte en selecteer **Eindpunten** aan de linkerkant onder het deelvenster **Assets**. 

1. Selecteer de implementatie die u wilt verwijderen en vervolgens **Verwijderen**. 

1. Selecteer **Doorgaan**.

### <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gebruikgemaakt van geautomatiseerde ML in de Azure Machine Learning Studio voor het maken en implementeren van een tijdreeks-prognosemodel waarmee de vraag naar bikeshare-verhuur wordt voorspeld. 

Raadpleeg dit artikel voor stappen voor het maken van een door Power BI ondersteund schema om het gebruik van uw pas geïmplementeerde webservice te faciliteren:

> [!div class="nextstepaction"]
> [Een webservice gebruiken](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ Meer informatie over [geautomatiseerde machine learning](concept-automated-ml.md).
+ Raadpleeg het artikel [Geautomatiseerde machine learning-resultaten begrijpen](how-to-understand-automated-ml.md) voor meer informatie over metrische classificatiegegevens en grafieken.
+ Meer informatie over [featurization](how-to-configure-auto-features.md#featurization).
+ Meer informatie over [gegevensprofilering](how-to-connect-data-ui.md#profile).

>[!NOTE]
> Deze bikeshare-gegevensset is gewijzigd voor deze zelfstudie. Deze gegevensset is beschikbaar gemaakt als onderdeel van een [Kaggle-wedstrijd](https://www.kaggle.com/c/bike-sharing-demand/data) en was oorspronkelijk beschikbaar via [Capital Bikeshare](https://www.capitalbikeshare.com/system-data). Hij kan ook worden gevonden in de [UCI Machine Learning-database](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset).<br><br>
> Bron: Fanaee-T, Hadi, en Gama, Joao, Event labeling combining ensemble detectors and background knowledge, Progress in Artificial Intelligence (2013): pp. 1-15, Springer Berlin Heidelberg.
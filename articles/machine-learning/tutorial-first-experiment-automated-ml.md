---
title: Geautomatiseerde ML-classificatiemodellen maken
titleSuffix: Azure Machine Learning
description: Ontdek hoe u classificatiemodellen kunt trainen en implementeren met een geautomatiseerde machine learning-interface (geautomatiseerde ML) van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: f0bb354bce0c4696f60e2be5c6186760518c7431
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549183"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Zelfstudie: Een classificatiemodel maken met geautomatiseerde ML in Azure Machine Learning


In deze zelfstudie leert u hoe u een eenvoudig classificatiemodel maakt zonder ook maar één regel code te schrijven, met de geautomatiseerde machine learning in de Azure Machine Learning Studio. Dit classificatiemodel voorspelt of een klant een termijnrekening zal openen bij een financiële instelling.

Met geautomatiseerde machine learning kunt u tijdsintensieve taken automatiseren. Geautomatiseerde machine learning doorloopt of itereert snel allerlei combinaties van algoritmen en hyperparameters om het beste model te vinden op basis van uw maatstaaf voor succes.

Voor een voorbeeld van tijdreeksprognoses, zie [ Zelfstudie: Vraagprognose en AutoML](tutorial-automated-ml-forecast.md).

In deze zelfstudie leert u hoe u de volgende taken uitvoert:

> [!div class="checklist"]
> * Een Azure Machine Learning-werkruimte maken.
> * Een experiment voor geautomatiseerde machine learning uitvoeren.
> * Experimentgegevens bekijken.
> * Het model implementeren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://aka.ms/AMLFree) aan.

* Download het gegevensbestand [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv). De kolom **y** geeft aan of een klant een termijnrekening heeft geopend. Verderop in deze zelfstudie wordt deze geïdentificeerd als doelkolom voor voorspellingen. 

## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werkruimte is een basisblok in de cloud dat u gebruikt voor het experimenteren, trainen en implementeren van machine learning-modellen. De klasse bindt uw Azure-abonnement en resourcegroep aan een eenvoudig te verbruiken object in de service. 

Er zijn verschillende manieren om een [werkruimte te maken](how-to-manage-workspace.md). In deze zelfstudie maakt u een werkruimte via Azure Portal, een webconsole om uw Azure-resources te beheren.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Noteer uw **werkruimte** en **abonnement**. U hebt deze nodig om ervoor te zorgen dat u uw experiment op de juiste plek maakt. 

## <a name="get-started-in-azure-machine-learning-studio"></a>Aan de slag met Azure Machine Learning Studio

U doorloopt de volgende stappen voor het voorbereiden en uitvoeren van het experiment via de Azure Machine Learning Studio op https://ml.azure.com, een geconsolideerde webinterface met hulpmiddelen voor machine learning waar gegevenswetenschappers, ongeacht hun vaardigheidsniveaus, scenario's kunnen uitvoeren. De Studio wordt niet ondersteund in Internet Explorer-browsers.

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com).

1. Selecteer uw abonnement en de werkruimte die u hebt gecreëerd.

1. Selecteer **Aan de slag**.

1. Selecteer in het linkerdeelvenster **Geautomatiseerde ML** in de sectie **Maken**.

   Aangezien dit uw eerste experiment voor geautomatiseerde ML is, ziet u een lege lijst en koppelingen naar documentatie.

   ![Pagina Aan de slag](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Selecteer **Nieuwe geautomatiseerde ML-uitvoering**. 

## <a name="create-and-load-dataset"></a>Gegevensset maken en laden

Voordat u uw experiment gaat configureren, uploadt u uw gegevensbestand naar uw werkruimte in de vorm van een Azure Machine Learning-gegevensset. Als u dit doet, kunt u ervoor zorgen dat uw gegevens op de juiste wijze zijn opgemaakt voor uw experiment.

1. Maak een nieuwe gegevensset door **Uit lokale bestanden** te selecteren in de vervolgkeuzelijst **+Gegevensset maken**. 

    1. Geef uw gegevensset een naam en een optionele beschrijving in het formulier **Basisinformatie**. De geautomatiseerde ML-interface ondersteunt momenteel enkel TabularDatasets. Het type gegevensset moet dus standaard op *In tabelvorm* staan.

    1. Selecteer **Volgende** in de linkerbenedenhoek

    1. Selecteer, in het formulier **Gegevensarchief- en bestandsselectie** het standaard gegevensarchief dat automatisch werd ingesteld bij het aanmaken van uw werkruimte, **workspaceblobstore (Azure Blob Storage)** . Hier uploadt u uw gegevensbestand om het beschikbaar te maken voor uw werkruimte.

    1. Selecteer **Bladeren**.
    
    1. Kies het bestand **bankmarketing_train.csv** op uw lokale computer. Dit is het bestand dat u hebt gedownload als [vereiste](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Geef uw gegevensset een naam en een optionele beschrijving. 

    1. Selecteer **Volgende** linksonder om het bestand te uploaden naar de standaardcontainer die automatisch werd opgezet bij het aanmaken van de werkruimte.  
    
       Wanneer het uploaden is voltooid, worden de instellingen en het voorbeeldformulier automatisch ingevuld op basis van het bestandstype. 
       
    1. Controleer of het formulier **Instellingen en voorbeeld** als volgt is ingevuld en selecteer **Volgende**.
        
        Veld|Beschrijving| Waarde voor zelfstudie
        ---|---|---
        Bestandsindeling|Definieert de indeling en het type gegevens dat is opgeslagen in een bestand.| Met scheidingstekens
        Scheidingsteken|Een of meer tekens die de grens aangeven tussen &nbsp; afzonderlijke, onafhankelijke regio's in tekst zonder opmaak of andere gegevensstromen. |Komma
        Encoding|Identificeert welke bit-naar-tekenschematabel er moet gebruikt worden om uw gegevensset te lezen.| UTF-8
        Kolomkoppen| Geeft aan hoe eventuele koppen van de gegevensset worden behandeld.| Alle bestanden hebben dezelfde koppen
        Rijen overslaan | Geeft aan hoeveel rijen er eventueel worden overgeslagen in de gegevensset.| Geen

    1. Met het formulier **Schema** kunt u uw gegevens verder configureren voor dit experiment. Voor dit voor beeld selecteert u de wissel knop voor de **day_of_week**, zodat u deze niet kunt gebruiken. Selecteer **Next**.
         ![Schema formulier](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)
    1. Controleer of de informatie in het formulier **Details bevestigen** overeenkomt met wat voorheen in de formulieren **Basisinformatie, Gegevensarchief en bestandselectie** en **Instellingen en voorbeeld** is ingevuld.
    
    1. Selecteren **Maken** om uw gegevensset te voltooien.
    
    1. Selecteer uw gegevensset wanneer deze verschijnt in de lijst.
    
    1. Controleer het **Voorbeeld van de gegevens** om te controleren of u **day_of_week** niet hebt  opgenomen, en selecteer **Sluiten**.

    1. Selecteer **Volgende**.

## <a name="configure-run"></a>Uitvoering configureren

Nadat u uw gegevens hebt geladen en geconfigureerd, kunt u uw experiment instellen. Dit installatieprogramma bevat ontwerptaken voor het experiment, zoals, het selecteren van de grootte van uw rekenomgeving en het opgeven van de kolom die u wilt voorspellen. 

1. Selecteer het keuzerondje **Nieuwe maken**.

1. Vul het formulier **Uitvoering configureren** als volgt in:
    1. Voer de naam van het nieuwe experiment in: `my-1st-automl-experiment`

    1. Selecteer **y** als doelkolom, wat u wilt voorspellen. Deze kolom geeft aan of de klant een termijnrekening heeft geopend of niet.
    
    1. Selecteer **+ Een nieuwe berekening maken** en configureer uw rekendoel. Een rekendoel is een resource-omgeving, lokaal of in de cloud, die gebruikt wordt om uw trainingsscript uit te voeren of uw service-implementatie te hosten. Voor dit experiment gebruiken we berekening in de cloud. 
        1. Vul het formulier **Virtuele machine** in om de berekening in te stellen.

            Veld | Beschrijving | Waarde voor zelfstudie
            ----|---|---
            Prioriteit van virtuele&nbsp;machine&nbsp; |Selecteer de prioriteit die het experiment moet krijgen| Toegewezen
            Virtuele&nbsp;machine&nbsp;type| Selecteer het type van de virtuele machine voor uw berekening.|CPU (Central Processing Unit, centrale verwerkingseenheid)
            Grootte&nbsp;virtuele&nbsp;machine| Selecteer de grootte van de virtuele machine voor uw berekening. Er wordt een lijst met aanbevolen grootten geboden, op basis van uw gegevens en het type experiment. |Standard_DS12_V2
        
        1. Selecteer **Volgende** om het **formulier Instellingen configureren** in te vullen.
        
            Veld | Beschrijving | Waarde voor zelfstudie
            ----|---|---
            Naam berekening |  Een unieke naam die de context van uw berekening identificeert. | automl-compute
            Min / Max knooppunten| U moet u één of meer knooppunten opgeven om gegevens te profileren.|Min. knooppunten: 1<br>Max. knooppunten: 6
            Seconden wachten voor omlaag schalen | Niet-actieve tijd voordat het cluster automatisch omlaag wordt geschaald naar het minimum aantal knooppunten.|120 (standaardinstelling)
            Geavanceerde instellingen | Instellingen voor het configureren en autoriseren van een virtueel netwerk voor uw experiment.| Geen               

        1. Selecteer **Maken** om uw rekendoel te maken. 

            **Dit duurt enkele minuten.** 

             ![Pagina Instellingen](./media/tutorial-first-experiment-automated-ml/compute-settings.png)

        1. Wanneer dit is voltooid, selecteert u uw nieuwe rekendoel uit de vervolgkeuzelijst.

    1. Selecteer **Next**.

1. Voltooi de installatie voor uw automatische ML-experiment op het formulier **Taaktype en -instellingen** door het taaktype en de configuratie-instellingen van de machine learning op te geven.
    
    1.  Selecteer **Classificatie** als het machine learning-taaktype.

    1. Selecteer **Aanvullende configuratie-instellingen weergeven** en vul de velden als volgt in. Dankzij deze instellingen kunt u de trainingstaak beter controleren. Anders worden de standaardinstellingen toegepast op basis van de selectie en gegevens van het experiment.

        Aanvullende&nbsp;configuraties|Beschrijving|Waarde&nbsp;voor&nbsp;zelfstudie
        ------|---------|---
        Primaire metrische gegevens| Evaluatiewaarde waarmee het machine learning-algoritme wordt gemeten.|AUC_weighted
        Uitleg geven over het beste model| Hiermee wordt automatisch uitleg gegeven over het beste model dat is gemaakt met geautomatiseerde ML.| Inschakelen
        Geblokkeerde algoritmen | Algoritmen die u niet wilt opnemen in de trainingstaak| Geen
        Criterium voor afsluiten| Als er aan een criterium is voldaan, wordt de trainingstaak gestopt. |Tijd voor&nbsp;trainingstaak&nbsp; (uur): 1 <br> Drempelwaarde voor metrische &nbsp;score&nbsp;: Geen
        Validatie | Kies een kruisvalidatietype en een aantal tests.|Validatietype:<br>&nbsp;k-voudige&nbsp;kruisvalidatie <br> <br> Aantal validaties: 2
        Gelijktijdigheid| Het maximum aantal parallelle iteraties uitgevoerd per iteratie| Maximum aantal&nbsp;gelijktijdige&nbsp;iteraties: 5
        
        Selecteer **Opslaan**.
    
1. Selecteer **Voltooien** om het experiment uit te voeren. Het scherm **Details uitvoering** opent en de **Uitvoeringsstatus** wordt bovenaan weergegeven terwijl de voorbereidingen voor het experiment beginnen. Deze status wordt bijgewerkt wanneer het experiment wordt uitgevoerd. Meldingen worden ook weergegeven in de rechterbovenhoek van Studio, zodat u op de hoogte blijft van de status van het experiment.

>[!IMPORTANT]
> Het duurt **10-15 minuten** om de experimentele uitvoerbewerking voor te bereiden.
> Zodra de uitvoering is gestart duurt het **2-3 minuten langer per iteratie**.  <br> <br>
> Bij een productie zou u waarschijnlijk even weggaan. Maar voor deze zelfstudie raden we aan om onder het tabblad **Modellen** de geteste algoritmen te bekijken die voltooid zijn terwijl de andere nog worden uitgevoerd. 

##  <a name="explore-models"></a>Modellen bekijken

Ga naar het tabblad **Modellen** om de geteste algoritmen (modellen) te bekijken. De modellen worden standaard gerangschikt op hun metrische score terwijl ze worden voltooid. Voor deze zelfstudie staat het model dat het hoogst scoort op basis van de gekozen metrische waarde **AUC_weighted** bovenaan de lijst.

Terwijl u wacht tot alle experimentmodellen voltooid zijn, kunt u de **Algoritmenaam** van een volledig model selecteren om de prestatiedetails te bekijken. 

In het volgende gedeelte kunt u naar de tabbladen **Details** en **Metrische gegevens** gaan om de eigenschappen, metrische gegevens en prestatiegrafieken van het geselecteerde model te bekijken. 

![Details van uitvoeringsiteratie](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>Het beste model implementeren

In de geautomatiseerde machine learning-interface kunt u met enkele stappen het beste model implementeren als webservice. Implementatie is de integratie van het model zodat het nieuwe gegevens kan voorspellen en potentiële kansgebieden kan identificeren. 

Voor dit experiment betekent de implementatie naar een webservice dat de financiële instelling nu een iteratieve en schaalbare weboplossing heeft om potentiële klanten voor termijnrekeningen te identificeren. 

Controleer of de uitvoering van uw experimentele uitvoerbewerking is voltooid. Om dit te doen, gaat u terug naar de bovenliggende uitvoeringspagina door **1 uitvoeren** aan de bovenkant van het scherm te selecteren. In de linkerbovenhoek van het scherm wordt de status **Voltooid** weergegeven. 

Zodra de uitvoering van het experiment is voltooid, wordt op de pagina **Details** een sectie met **Beste modeloverzicht** ingevuld. In de context van dit experiment is **VotingEnsemble** het beste model op basis van de metrische waarde **AUC_weighted**.  

We implementeren dit model, maar houd er rekening mee dat implementatie ongeveer 20 minuten duurt. Het implementatieproces omvat verschillende stappen, waaronder het model registreren, resources genereren en ze configureren voor de webservice.

1. Selecteer **VotingEnsemble** om de model-specifieke pagina te openen.

1. Selecteer de knop **Implementeren** in de linkerbovenhoek.

1. Vul het deelvenster **Een model implementeren** als volgt in:

    Veld| Waarde
    ----|----
    Naam van implementatie| my-automl-deploy
    Beschrijving van implementatie| Implementatie van mijn eerste geautomatiseerde machine learning-experiment
    Rekentype | Azure Compute Instance (ACI) selecteren
    Verificatie inschakelen| Uitgeschakeld. 
    Aangepaste implementaties gebruiken| Uitgeschakeld. Staat toe dat het standaard stuurprogrammabestand (scorescript) en het omgevingsbestand automatisch gegenereerd worden. 
    
    In dit voorbeeld gebruiken we de standaardwaarden in het menu *Geavanceerd*. 

1. Selecteer **Implementeren**.  

    Er verschijnt een groen succesbericht bovenaan het scherm **Uitvoering**. In het deelvenster **Modeloverzicht** verschijnt een statusbericht onder **Implementatiestatus**. Selecteer regelmatig **Vernieuwen** om de implementatiestatus te controleren.
    
U hebt nu een operationele webservice om voorspellingen te genereren. 

Ga verder met de [**Volgende stappen**](#next-steps) voor meer informatie over het gebruik van uw nieuwe webservice en test uw voorspellingen met de ingebouwde ondersteuning voor Azure Machine Learning van Power BI.

## <a name="clean-up-resources"></a>Resources opschonen

Implementatiebestanden zijn groter dan gegevens- en experimentbestanden. Daarom kost het meer om ze op te slaan. Verwijder alleen de implementatiebestanden om de kosten voor uw account te beperken, of als u uw werkruimte en experimentbestanden wilt behouden. Zo niet, verwijder dan de volledige resourcegroep als u geen enkel bestand wilt gebruiken.  

### <a name="delete-the-deployment-instance"></a>Het implementatie-exemplaar verwijderen

Verwijder alleen het implementatie-exemplaar van Azure Machine Learning op https:\//ml.azure.com/ indien u de resourcegroep en werkruimte wilt behouden voor andere zelfstudies en verkenning. 

1. Ga naar [Azure Machine Learning](https://ml.azure.com/). Ga naar uw werkruimte en selecteer **Eindpunten** aan de linkerkant onder het deelvenster **Activa**. 

1. Selecteer de implementatie die u wilt verwijderen en vervolgens **Verwijderen**. 

1. Selecteer **Doorgaan**.

### <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie over geautomatiseerde machine learning heeft u de geautomatiseerde ML-interface van Azure Machine Learning gebruikt om een classificatiemodel te maken en implementeren. Raadpleeg de volgende artikelen voor meer informatie en de volgende stappen:

> [!div class="nextstepaction"]
> [Een webservice gebruiken](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ Meer informatie over [geautomatiseerde machine learning](concept-automated-ml.md).
+ Raadpleeg het artikel [Geautomatiseerde machine learning-resultaten begrijpen](how-to-understand-automated-ml.md) voor meer informatie over metrische classificatiegegevens en grafieken.
+ Meer informatie over [featurization](how-to-configure-auto-features.md#featurization).
+ Meer informatie over [gegevensprofilering](how-to-connect-data-ui.md#profile).


>[!NOTE]
> Deze gegevensset voor marketing van een bank is beschikbaar onder de [Creative Commons-licentie CCO: Public Domain)](https://creativecommons.org/publicdomain/zero/1.0/). Alle rechten voor individuele inhoudselementen van de database zijn gelicentieerd onder de [Database Contents License](https://creativecommons.org/publicdomain/zero/1.0/) en zijn beschikbaar op [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Deze gegevensset was oorspronkelijk beschikbaar binnen de [UCI Machine Learning Database](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez en P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, juni 2014.

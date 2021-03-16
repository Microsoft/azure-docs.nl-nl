---
title: 'ML Studio (klassiek): migreren naar Azure Machine Learning'
description: Migreren van Studio (klassiek) naar Azure Machine Learning voor een modern data Science-platform.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: fda34a7ee06d35846bcec571e904297d0421c38f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564937"
---
# <a name="migrate-to-azure-machine-learning"></a>Migreren naar Azure Machine Learning

In dit artikel leert u hoe u Studio-assets (Classic) kunt migreren naar Azure Machine Learning. Op dit moment moet u uw experimenten hand matig opnieuw maken om resources te migreren.

Azure Machine Learning biedt een gemodern data Science platform dat de eerste benaderingen zonder code en code combineert. Zie de sectie [Azure machine learning beoordelen](#step-1-assess-azure-machine-learning) voor meer informatie over de verschillen tussen Studio (klassiek) en Azure machine learning.


## <a name="recommended-approach"></a>Aanbevolen benadering

Als u wilt migreren naar Azure Machine Learning, raden we u aan de volgende aanpak uit te voeren:

> [!div class="checklist"]
> * Stap 1: Azure Machine Learning evalueren
> * Stap 2: een migratie plan maken
> * Stap 3: experimenten en Web Services opnieuw samen stellen
> * Stap 4: client-apps integreren
> * Stap 5: het opschonen van Studio-assets (klassiek)


## <a name="step-1-assess-azure-machine-learning"></a>Stap 1: Azure Machine Learning evalueren
1. Meer informatie over [Azure machine learning](https://azure.microsoft.com/services/machine-learning/); het gaat om voor delen, kosten en architectuur.

1. Vergelijk de mogelijkheden van Azure Machine Learning en Studio (klassiek).

    >[!NOTE]
    > De functie **Designer** in azure machine learning biedt een vergelijk bare functionaliteit voor het slepen en neerzetten van Studio (klassiek). Azure Machine Learning biedt echter ook robuuste [code-eerste werk stromen](../concept-model-management-and-deployment.md) als alternatief. Deze migratie reeks is gericht op de ontwerp functie, omdat deze het meest overeenkomt met de Studio-ervaring.

    [!INCLUDE [aml-compare-classic](../../../includes/machine-learning-compare-classic-aml.md)]

3. Controleer of uw essentiële Studio-modules (Classic) worden ondersteund in Azure Machine Learning Designer. Zie voor meer informatie de onderstaande [Studio-en ontwerp module-toewijzings](#studio-classic-and-designer-module-mapping) tabel.

4. [Een Azure Machine Learning-werkruimte maken](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace?tabs=azure-portal)

## <a name="step-2-create-a-migration-plan"></a>Stap 2: een migratie plan maken

1. Identificeer de Studio- **gegevens sets**, **modellen** en **webservices** die u wilt migreren.

1. Bepaal wat het effect is van een migratie op uw bedrijf.

1. Een migratie plan maken.

## <a name="step-3-rebuild-experiments-and-web-services"></a>Stap 3: experimenten en Web Services opnieuw samen stellen

1. [Gegevens sets migreren naar Azure machine learning](migrate-register-dataset.md).
1. Gebruik de Designer om [experimenten opnieuw samen te stellen](migrate-rebuild-experiment.md).
1. Gebruik de Designer om [webservices](migrate-rebuild-web-service.md)opnieuw te implementeren.

    >[!NOTE]
    > Azure Machine Learning biedt ook ondersteuning voor code-First-werk stromen voor [gegevens sets](../how-to-create-register-datasets.md), [training](../how-to-set-up-training-targets.md)en [implementatie](../how-to-deploy-and-where.md).

## <a name="step-4-integrate-client-apps"></a>Stap 4: client-apps integreren

1. Wijzig client toepassingen die Studio-webservices aanroepen om uw nieuwe [Azure machine learning-eind punten](migrate-rebuild-integrate-with-client-app.md)te gebruiken.

## <a name="step-5-cleanup-studio-classic-assets"></a>Stap 5: opschonen Studio-assets (klassiek)

1. Het [opschonen van Studio-assets (klassiek)](export-delete-personal-data-dsr.md) om extra kosten te voor komen. Mogelijk wilt u activa behouden voor terugval totdat u Azure Machine Learning workloads hebt gevalideerd.

## <a name="studio-classic-and-designer-module-mapping"></a>Module voor Studio (klassiek) en Designer-toewijzing

Raadpleeg de volgende tabel om te zien welke modules u moet gebruiken tijdens het opnieuw opbouwen van Studio-experimenten (klassiek) in de ontwerp functie.


> [!IMPORTANT]
> De Designer implementeert modules via open source Python-pakketten in plaats van C#-pakketten zoals Studio (klassiek). Vanwege dit verschil kan de uitvoer van ontwerp modules enigszins verschillen van de tegen Hangers van Studio (Classic).


|Categorie|Studio-module (klassiek)|Vervangende ontwerp module|
|--------------|----------------|--------------------------------------|
|Invoer en uitvoer van gegevens|-Gegevens hand matig invoeren </br> -Gegevens exporteren </br> -Gegevens importeren </br> -Getraind model laden </br> -Gezipte gegevens sets uitpakken|-Gegevens hand matig invoeren </br> -Gegevens exporteren </br> -Gegevens importeren|
|Conversies van gegevens indeling|-Converteren naar CSV </br> -Converteren naar gegevensset </br> -Converteren naar ARFF </br> -Converteren naar SVMLight </br> -Converteren naar TSV|-Converteren naar CSV </br> -Converteren naar gegevensset|
|Gegevens transformatie-manipulatie|-Kolommen toevoegen</br> -Rijen toevoegen </br> -SQL-trans formatie Toep assen </br> -Ontbrekende gegevens reinigen </br> -Converteren naar indicator waarden </br> -Meta gegevens bewerken </br> -Gegevens samen voegen </br> -Dubbele rijen verwijderen </br> -Kolommen selecteren in gegevensset </br> -Kolom transformatie selecteren </br> -SMOTE </br> -Groep categorische waarden|-Kolommen toevoegen</br> -Rijen toevoegen </br> -SQL-trans formatie Toep assen </br> -Ontbrekende gegevens reinigen </br> -Converteren naar indicator waarden </br> -Meta gegevens bewerken </br> -Gegevens samen voegen </br> -Dubbele rijen verwijderen </br> -Kolommen selecteren in gegevensset </br> -Kolom transformatie selecteren </br> -SMOTE|
|Gegevens transformatie: schalen en verminderen |-Clip waarden </br> -Gegevens in opslag locaties groeperen </br> -Gegevens normaliseren </br>-Principal-onderdeel analyse |-Clip waarden </br> -Gegevens in opslag locaties groeperen </br> -Gegevens normaliseren|
|Gegevens transformatie: voor beeld en splitsing|-Partitie en voor beeld </br> -Gegevens splitsen|-Partitie en voor beeld </br> -Gegevens splitsen|
|Gegevens transformatie – filter |-Filter Toep assen </br> -FIR-filter </br> -IIR-filter </br> Filter Mediaan </br> -Zwevend gemiddelde filter </br> -Drempel filter </br> -Door de gebruiker gedefinieerd filter||
|Gegevens transformatie – leren met aantallen |-Trans formatie voor het tellen van builds </br> -Tabel export aantal </br> -Tabel voor het importeren van aantallen </br> -Trans formatie aantal samen voegingen</br>  -Para meters voor het aantal tabellen wijzigen||
|Functie selectie |-Functies selecteren op basis van filter </br> -Fisher lineaire discriminant analyse  </br> -Het belang van de functie van permutatie |-Functies selecteren op basis van filter </br>  -Het belang van de functie van permutatie|
| Model-classificatie| -Forest voor de beslissing van multi klassen </br> -Beslissings jungle met meer klassen  </br> -Regressie van multiklasse-logistiek  </br>-Netwerk met Multi Class Neural  </br>-One-vs-All Multiclass </br>-Two-Class gemiddelde Perceptron </br>-Two-Class Bayes-punt machine </br>-Two-Class versterkte beslissings structuur  </br> -Two-Class beslissings forest  </br> -Two-Class beslissing jungle  </br> -Two-Class Locally-Deep SVM </br> -Two-Class logistiek regressie  </br> -Two-Class Neural-netwerk </br> -Two-Class Support Vector machine  | -Forest voor de beslissing van multi klassen </br>  -Beslissings structuur voor het boosten van multi klassen  </br> -Regressie van multiklasse-logistiek </br> -Netwerk met Multi Class Neural </br> -One-vs-All Multiclass  </br> -Two-Class gemiddelde Perceptron  </br> -Two-Class versterkte beslissings structuur  </br> -Two-Class beslissings forest </br>-Two-Class logistiek regressie </br> -Two-Class Neural-netwerk </br>-Two-Class Support Vector machine  |
| Model-clustering| -K-means clustering| -K-means clustering|
| Model-regressie| -Bayesiaanse lineaire regressie  </br> -Regressie verbetering van de beslissings structuur  </br>-Regressie voor beslissings bossen  </br> -Snelle Quantile-regressie voor forests  </br> -Lineaire regressie </br> -Neural netwerk regressie </br> -Regressie van de regressie van het rang telwoord| -Regressie verbetering van de beslissings structuur  </br>-Regressie voor beslissings bossen  </br> -Snelle Quantile-regressie voor forests </br> -Lineaire regressie  </br> -Neural netwerk regressie </br> -Poisson-regressie|
| Model-anomalie detectie| -One-Class SVM  </br> -PCA-Based anomalie detectie | -PCA-Based anomalie detectie|
| Machine Learning: evalueren  | -Kruis validatie model  </br>-Model evalueren  </br>-Aanbevolen test evalueren | -Kruis validatie model  </br>-Model evalueren </br> -Aanbevolen test evalueren|
| Machine Learning – Train| -Clusters opruimen  </br> -Model voor detectie van anomalieën trainen </br>-Cluster model trainen  </br> -Train matchbox-aanbevolen-</br> Model trainen  </br>-Model Hyper parameters afstemmen| -Model voor detectie van anomalieën trainen  </br> -Cluster model trainen </br> -Train model-</br> -PyTorch-model trainen  </br>-Train SVD-aanbevolen  </br>-Breed en diep aanbevolen trainen </br>-Model Hyper parameters afstemmen|
| Machine Learning-Score| -Trans formatie Toep assen  </br>-Gegevens toewijzen aan clusters  </br>-Matchbox-aanbevolen Score </br> -Score model|-Trans formatie Toep assen  </br> -Gegevens toewijzen aan clusters </br> -Afbeeldings model van Score  </br> -Score model </br>-SVD-aanbevolen Score </br> -Score breed en diep aanbevolen|
| OpenCV-bibliotheek modules| -Installatie kopieën importeren </br>-Classificatie van de vooraf getrainde trapsgewijze installatie kopieën | |
| Python-taal modules| -Python-script uitvoeren| -Python-script uitvoeren  </br> -Python-model maken |
| R-taal modules  | -R-script uitvoeren  </br> -R-model maken| -R-script uitvoeren|
| Statistische functies | -Mathematische bewerking Toep assen </br>-Elementaire statistieken berekenen  </br>-Lineaire correlatie berekenen  </br>-Kansdichtheids functie evalueren  </br>-Discrete waarden vervangen  </br>-Gegevens samenvatten  </br>-Test hypo these met t-test| -Mathematische bewerking Toep assen  </br>-Gegevens samenvatten|
| Text Analytics| -Talen detecteren  </br>-Sleutel zinnen uit tekst extra heren  </br>-N-gram-functies uit tekst extra heren  </br>-Hashing functie </br>-Latente Dirichlet toewijzing  </br>-Benoemde entiteit herkenning </br>-Tekst voorverwerken  </br>-Score Vowpal Wabbit versie 7-10-model  </br>-Score Vowpal Wabbit versie 8 model </br>-Train Vowpal Wabbit versie 7-10-model  </br>-Train Vowpal Wabbit versie 8 model |-Converteer het woord naar een vector </br> -N-gram-functies uit tekst extra heren </br>-Hashing functie  </br>-Latente Dirichlet toewijzing </br>-Tekst voorverwerken  </br>-Score Vowpal Wabbit model </br> -Vowpal Wabbit-model trainen|
| Tijd reeks| -Detectie van afwijkings tijd Series | |
| Webservice | -Invoer </br> -Uitvoer | -Invoer </br>  - Uitvoer|
| Computer Vision| | -Afbeeldings transformatie Toep assen </br> -Converteren naar afbeelding map </br> -Trans formatie van init-afbeelding </br> -Map voor afbeeldingen splitsen  </br> -DenseNet-installatie kopie classificatie   </br>-ResNet-installatie kopie classificatie |

Voor meer informatie over het gebruik van afzonderlijke ontwerp modules raadpleegt u de naslag informatie voor de [module Designer](../algorithm-module-reference/module-reference.md).

### <a name="what-if-a-designer-module-is-missing"></a>Wat moet ik doen als een ontwerp module ontbreekt?

Azure Machine Learning Designer bevat de populairste modules van Studio (klassiek). Het bevat ook nieuwe modules die gebruikmaken van de nieuwste machine learning-technieken. 

Als uw migratie wordt geblokkeerd vanwege ontbrekende modules in de ontwerp functie, neemt u contact met ons op door [een ondersteunings ticket te maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="example-migration"></a>Voorbeeld migratie

De volgende proef migratie markeert enkele van de verschillen tussen Studio (klassiek) en Azure Machine Learning.

### <a name="datasets"></a>Gegevenssets

In Studio (klassiek) zijn **gegevens sets** in uw werk ruimte opgeslagen en kunnen ze alleen door Studio (klassiek) worden gebruikt.

![Auto Mobile-prijs-klassiek-gegevensset](./media/migrate-overview/studio-classic-dataset.png)

In Azure Machine Learning worden **gegevens sets** geregistreerd in de werk ruimte en kunnen ze worden gebruikt in alle Azure machine learning. Zie [beveiligde gegevens toegang](../concept-data.md#reference-data-in-storage-with-datasets)voor meer informatie over de voor delen van Azure machine learning gegevens sets.

![Auto Mobile-prijs-AML-gegevensset](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>Pijplijn

In Studio (klassiek) bevatten **experimenten** de verwerkings logica voor uw werk. U hebt experimenten gemaakt met modules voor slepen en neerzetten.


![Auto Mobile-prijs-klassiek-experiment](./media/migrate-overview/studio-classic-experiment.png)

In Azure Machine Learning bevatten **pijp lijnen** de verwerkings logica voor uw werk. U kunt pijp lijnen maken met behulp van de modules voor slepen en neerzetten of door code te schrijven.

![Auto Mobile-prijs-AML-pijp lijn](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>Webservice-eind punt

In Studio (klassiek) werd de **API Request/respond** gebruikt voor realtime-voor spellingen. De **API voor batch uitvoering** is gebruikt voor batch voorspelling of retraining.

![Auto Mobile-Price-Classic-webservice](./media/migrate-overview/studio-classic-web-service.png)

In Azure Machine Learning worden **realtime-eind punten** gebruikt voor realtime-voor spellingen. **Pijplijn eindpunten** worden gebruikt voor batch voorspelling of retraining.

![Auto Mobile-prijs-AML-eind punt](./media/migrate-overview/aml-endpoint.png)


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de vereisten voor het migreren naar Azure Machine Learning geleerd. Zie voor gedetailleerde stappen de andere artikelen in de migratie reeks Studio (klassiek):

1. **Overzicht migratie**.
1. [Gegevensset migreren](migrate-register-dataset.md).
1. [Bouw een studio-trainings pijplijn (klassiek) opnieuw](migrate-rebuild-experiment.md)op.
1. [Een studio-webservice (klassiek) opnieuw bouwen](migrate-rebuild-web-service.md).
1. [Een Azure machine learning-webservice integreren met client-apps](migrate-rebuild-integrate-with-client-app.md).
1. [Execute R-script migreren](migrate-execute-r-script.md).







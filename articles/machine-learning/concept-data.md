---
title: Toegang tot gegevens in de Cloud beveiligen
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u veilig verbinding maakt met uw gegevens opslag in azure met Azure Machine Learning gegevens opslag en gegevens sets.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 08/31/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 601be8409db22162a410d481e6609d378718a7b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503586"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Beveiligde toegang tot gegevens in Azure Machine Learning

Met Azure Machine Learning kunt u eenvoudig verbinding maken met uw gegevens in de Cloud. Het biedt een abstracte laag voor de onderliggende opslag service, zodat u veilig toegang hebt tot uw gegevens en deze kunt gebruiken zonder dat u code hoeft te schrijven die specifiek zijn voor uw opslag type. Azure Machine Learning biedt ook de volgende gegevens mogelijkheden:

*    Interoperabiliteit met Panda-en Spark-DataFrames
*    Versie beheer en het bijhouden van gegevens afkomst
*    Gegevens labelen 
*    Bewaking van gegevensafwijking
    
## <a name="data-workflow"></a>Gegevens werk stroom

Wanneer u klaar bent voor het gebruik van de gegevens in uw opslag oplossing op basis van de Cloud, raden we u aan de volgende werk stroom voor het leveren van gegevens te gebruiken. In deze werk stroom wordt ervan uitgegaan dat u een [Azure-opslag account](../storage/common/storage-account-create.md?tabs=azure-portal) en gegevens hebt in een cloud-gebaseerde opslag service in Azure. 

1. Maak een [Azure machine learning gegevens opslag](#datastores) om verbindings gegevens op te slaan in uw Azure-opslag.

2. Maak vanuit dat gegevens archief een [Azure machine learning-gegevensset](#datasets) om te verwijzen naar een of meer specifieke bestanden in uw onderliggende opslag. 

3. U kunt deze gegevensset in uw machine learning-experiment gebruiken door
    1. Koppel deze aan het reken doel van het experiment voor model training.

        **OF** 

    1. Gebruik dit rechtstreeks in Azure Machine Learning oplossingen zoals automatische machine learning (geautomatiseerd ML) experiment, machine learning pijp lijnen of de [Azure machine learning Designer](concept-designer.md).

4. Maak [gegevensset-monitors](#drift) voor uw model-uitvoer gegevensset om te detecteren voor gegevens drift. 

5. Als er gegevens drift wordt gedetecteerd, werkt u uw invoer gegevensset bij en traint u uw model dienovereenkomstig opnieuw.

Het volgende diagram biedt een visuele demonstratie van deze aanbevolen werk stroom.

![Diagram toont de Azure Storage-service die naar een gegevens opslag doorloopt, die in een gegevensset loopt. De gegevensset stromen naar model training, die stromen naar gegevens drift, die terugstromen naar DataSet.](./media/concept-data/data-concept-diagram.svg)

<a name="datastores"></a>
## <a name="connect-to-storage-with-datastores"></a>Verbinding maken met opslag met data stores

Azure Machine Learning data stores beveiligen de verbindings gegevens veilig naar uw gegevens opslag in azure, zodat u deze niet in uw scripts hoeft te coderen. [Registreer en maak een gegevens opslag](how-to-access-data.md) om eenvoudig verbinding te maken met uw opslag account en om toegang te krijgen tot de gegevens in uw onderliggende opslag service. 

Ondersteunde cloud-gebaseerde opslag Services in azure die als gegevens opslag kunnen worden geregistreerd:

+ Azure Blob-container
+ Azure-bestandsshare
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks-bestandssysteem
+ Azure Database for MySQL

>[!TIP]
> De algemeen beschik bare functionaliteit voor het maken van opslag plaatsen vereist verificatie op basis van referenties voor toegang tot opslag Services, zoals een service-principal of SAS-token (Shared Access Signature). Deze referenties zijn toegankelijk voor gebruikers die *lezers* toegang tot de werk ruimte hebben. <br><br>Als dit een probleem is,  [maakt u een gegevens opslag die gebruikmaakt van op identiteit gebaseerde gegevens toegang tot Storage-services (preview)](how-to-identity-based-data-access.md). Deze mogelijkheid is een [experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functie en kan op elk gewenst moment worden gewijzigd.

<a name="datasets"></a>
## <a name="reference-data-in-storage-with-datasets"></a>Referentie gegevens in de opslag met gegevens sets

Azure Machine Learning gegevens sets geen kopieën zijn van uw data. Door een gegevensset te maken, maakt u een verwijzing naar de gegevens in de opslag service, samen met een kopie van de meta gegevens ervan. 

Omdat gegevens sets worden geëvalueerd als vertraagd, en de huidige locatie blijft bestaan, kunt u

* Geen extra opslag kosten in rekening gebracht.
* Geen risico dat u onbedoeld uw oorspronkelijke gegevens bronnen wijzigt.
* Verbeter de prestaties van de werk stroom snelheid.

Als u met uw gegevens in de opslag wilt werken, [maakt u een gegevensset](how-to-create-register-datasets.md) om uw gegevens te verpakken in een verbruikt object voor machine learning taken. Registreer de gegevensset voor uw werk ruimte om deze te delen en opnieuw te gebruiken in verschillende experimenten zonder complexiteit van gegevens opname.

Gegevens sets kunnen worden gemaakt op basis van lokale bestanden, open bare url's, [Azure open gegevens sets](https://azure.microsoft.com/services/open-datasets/)of Azure Storage-services via data stores. 

Er zijn twee typen gegevens sets: 

+ Een [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) verwijst naar één of meer bestanden in uw gegevens opslag of open bare url's. Als uw gegevens al zijn gereinigd en klaar zijn voor gebruik in trainings experimenten, kunt u [bestanden downloaden of koppelen](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) waarnaar wordt verwezen door FileDatasets naar uw reken doel.

+ Een [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) vertegenwoordigt gegevens in tabel vorm door het bestand of de lijst met bestanden te parseren. U kunt een TabularDataset laden in een Panda of Spark data frame voor verdere manipulatie en reiniging. Voor een volledige lijst met gegevens indelingen waaruit u TabularDatasets kunt maken, raadpleegt u de [klasse TabularDatasetFactory](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory).

Aanvullende gegevens sets kunnen worden gevonden in de volgende documentatie:

+ [Versie en track](how-to-version-track-datasets.md) gegevensset afkomst.
+ [Bewaak uw gegevensset](how-to-monitor-datasets.md) om te helpen bij het detecteren van gegevens drift.    

## <a name="work-with-your-data"></a>Werk met uw gegevens

Met gegevens sets kunt u een aantal machine learning taken uitvoeren via een naadloze integratie met Azure Machine Learning-functies. 

+ Een project voor het [labelen van gegevens](#label)maken.
+ Machine learning modellen trainen:
     + [automatische ML experimenten](how-to-use-automated-ml-for-ml-models.md)
     + de [ontwerper](tutorial-designer-automobile-price-train-score.md#import-data)
     + [notebooks](how-to-train-with-datasets.md)
     + [Azure Machine Learning pijp lijnen](./how-to-create-machine-learning-pipelines.md)
+ Toegang tot gegevens sets voor het afgeven van een [batch-deinterferentie](./tutorial-pipeline-batch-scoring-classification.md) in [machine learning pijp lijnen](./how-to-create-machine-learning-pipelines.md).
+ Stel een monitor voor gegevensset in voor de detectie van [gegevens drift](#drift) .

<a name="label"></a>

## <a name="label-data-with-data-labeling-projects"></a>Label gegevens met gegevens label projecten

Het labelen van grote hoeveel heden gegevens is vaak een hoofd in machine learning projecten. Voor degenen met een computer vision-onderdeel, zoals een afbeeldings classificatie of object detectie, zijn in het algemeen duizenden afbeeldingen en de bijbehorende labels vereist.

Azure Machine Learning biedt u een centrale locatie voor het maken, beheren en bewaken van label projecten. Het labelen van projecten helpt bij het coördineren van de gegevens, labels en team leden, zodat u de taken voor het label efficiënter kunt beheren. Momenteel ondersteunde taken zijn afbeeldings classificatie, ofwel meerdere labels, meerdere klassen en object-id's die gebruikmaken van begrensde vakken.

Maak een [gegevens label project](how-to-create-labeling-projects.md)en uitvoer een gegevensset voor gebruik in machine learning experimenten.

<a name="drift"></a>

## <a name="monitor-model-performance-with-data-drift"></a>Model prestaties bewaken met gegevens drift

In de context van machine learning is gegevens drift de wijziging in model invoer gegevens die leidt tot het model leren van prestaties. Het is een van de belangrijkste redenen om de nauw keurigheid van het model te verslechteren, waardoor gegevens drift helpt bij het detecteren van prestatie problemen van modellen.

Zie het artikel [een gegevensset maken](how-to-monitor-datasets.md) voor meer informatie over het detecteren en waarschuwen van gegevens drift op nieuwe gegevens in een gegevensset.

## <a name="next-steps"></a>Volgende stappen 

+ Maak een gegevensset in Azure Machine Learning Studio of met de python-SDK [met behulp van deze stappen.](how-to-create-register-datasets.md)
+ Probeer gegevensset-trainings voorbeelden uit met onze [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
---
title: Gegevens opname & automatisering
titleSuffix: Azure Machine Learning
description: Meer informatie over de voor-en nadelen van de beschik bare gegevens opname opties voor het trainen van uw machine learning-modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: a096375e32e3d8a6760da88fe5ec86a70d364aff
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872092"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Opties voor gegevens opname van Azure Machine Learning werk stromen

In dit artikel vindt u informatie over de voor-en nadelen van opties voor gegevens opname die beschikbaar zijn met Azure Machine Learning. 

U kunt kiezen uit:
+ [Azure Data Factory](#azure-data-factory) pijp lijnen, speciaal gebouwd voor het extra heren, laden en transformeren van gegevens

+ [Azure machine learning PYTHON SDK](#azure-machine-learning-python-sdk), met een oplossing voor aangepaste code voor gegevens opname taken.

+ een combi natie van beide

Gegevens opname is het proces waarin ongestructureerde gegevens worden geëxtraheerd uit een of meer bronnen en vervolgens worden voor bereid voor de trainings machine learning modellen. Het is ook tijdrovend, vooral als hand matig wordt gedaan, en als u grote hoeveel heden gegevens uit meerdere bronnen hebt. Als u deze inspanning automatiseert, worden bronnen vrijgemaakt en kunnen uw modellen de meest recente en toepasselijke gegevens gebruiken.

## <a name="azure-data-factory"></a>Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) biedt systeem eigen ondersteuning voor de bewaking van gegevens bronnen en triggers voor gegevens opname pijplijnen.  

De volgende tabel bevat een overzicht van de voor-en nadelen voor het gebruik van Azure Data Factory voor uw werk stromen voor gegevens opname.

|Voordelen|Nadelen
---|---
Speciaal gebouwd om gegevens te extra heren, te laden en te transformeren.|Biedt momenteel een beperkte set Azure Data Factory pijplijn taken 
Hiermee kunt u gegevensgestuurde werk stromen maken voor het organiseren van gegevens verplaatsing en-trans formaties op schaal.|Duur om te bouwen en te onderhouden. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) van Azure Data Factory voor meer informatie.
Geïntegreerd met verschillende Azure-hulpprogram ma's zoals [Azure Databricks](../data-factory/transform-data-using-databricks-notebook.md) en [Azure functions](../data-factory/control-flow-azure-function-activity.md) | Voert geen systeem eigen scripts uit, in plaats daarvan afhankelijk van afzonderlijke Compute voor script uitvoeringen 
Systeem eigen ondersteuning voor door gegevens bron geactiveerde gegevens opname| 
Gegevens voorbereiding en model trainings processen zijn gescheiden.|
Afkomst mogelijkheid voor Inge sloten gegevens voor Azure Data Factory gegevens stromen|
Biedt een [gebruikers interface](../data-factory/quickstart-create-data-factory-portal.md) met lage code ervaring voor niet-script benaderingen |

Deze stappen en het volgende diagram illustreren de werk stroom voor gegevens opname van Azure Data Factory.

1. Gegevens uit de bronnen ophalen
1. Transformeer de gegevens en sla deze op in een uitvoer BLOB-container, die fungeert als gegevens opslag voor Azure Machine Learning
1. Met de voor bereide gegevens die worden opgeslagen, roept de Azure Data Factory-pijp lijn een training Machine Learning pijp lijn aan die de voor bereide gegevens voor model training ontvangt


    ![Opname van ADF-gegevens](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Meer informatie over het bouwen van een pijp lijn voor gegevens opname voor Machine Learning met [Azure Data Factory](how-to-data-ingest-adf.md).

## <a name="azure-machine-learning-python-sdk"></a>Azure Machine Learning python-SDK 

Met de [python-SDK](/python/api/overview/azure/ml)kunt u gegevens opname taken opnemen in een [Azure machine learning pijplijn](./how-to-create-machine-learning-pipelines.md) stap.

In de volgende tabel vindt u een overzicht van de voor-en hand leidingen voor het gebruik van de SDK en een stap van ML-pijp lijnen voor gegevens opname taken.

Voordelen| Nadelen
---|---
Uw eigen python-scripts configureren | Ondersteunt geen systeem eigen ondersteuning voor het activeren van gegevens bronnen. Vereist logische app-of Azure-functie-implementaties
Gegevens voorbereiding als onderdeel van elke model training-uitvoering|Ontwikkel vaardigheden vereist voor het maken van een script voor gegevens opname
Ondersteunt gegevens voorbereidings scripts op verschillende reken doelen, waaronder [Azure machine learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed) |Biedt geen gebruikers interface voor het maken van het opname mechanisme

In het volgende diagram bestaat de Azure Machine Learning-pijp lijn uit twee stappen: gegevens opname en model training. De stap gegevens opname omvat taken die kunnen worden uitgevoerd met behulp van python-bibliotheken en de python-SDK, zoals het extra heren van gegevens uit lokale/webbronnen en gegevens transformaties, zoals ontbrekende waarde-overlopende waarden. De trainings stap gebruikt vervolgens de voor bereide gegevens als invoer voor uw trainings script om uw machine learning model te trainen. 

![Azure-pijp lijn en SDK-gegevens opname](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Volgende stappen

Volg deze procedures:
* [Een pijp lijn voor gegevens opname bouwen met Azure Data Factory](how-to-data-ingest-adf.md)

* [Gegevens opname pijplijnen automatiseren en beheren met Azure-pijp lijnen](how-to-cicd-data-ingestion.md).
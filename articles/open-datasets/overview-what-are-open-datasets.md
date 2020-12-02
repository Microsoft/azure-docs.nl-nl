---
title: Wat zijn open gegevenssets? Gecureerde openbare gegevenssets
titleSuffix: Azure Open Datasets
description: Meer informatie over Azure Open Datasets, gecureerde gegevenssets van het openbare domein, zoals weer, tellingen, vakanties en locaties om voorspellende oplossingen te verbeteren.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: cc7d3a7ba5927730a68b704d61101a9dba447f2f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493964"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Wat zijn Azure Open Datasets en hoe kunt u ze gebruiken?

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) zijn samengestelde openbare gegevenssets die u kunt gebruiken om scenariospecifieke functies toe te voegen aan machine learning-oplossingen voor nauwkeurigere modellen. Open Datasets bevinden zich in de cloud in Microsoft Azure en worden geïntegreerd in Azure Machine Learning en zijn beschikbaar voor Azure Databricks en Machine Learning Studio (klassiek). U kunt de gegevenss ook openen via API's en die gebruiken in andere producten, zoals Power BI en Azure Data Factory.

Gegevenssets omvatten gegevens uit het openbare domein voor weer, tellingen, vakanties, publieke veiligheid en locaties die u helpen machine learning-modellen te trainen en voorspellende oplossingen te verrijken. U kunt uw openbare gegevenssets ook delen in Azure Open Datasets. 

![Azure Open Datasets-onderdelen](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Gecureerde, voorbereide gegevenssets
Gecureerde openbare gegevenssets in Azure Open Datasets zijn geoptimaliseerd voor gebruik in machine learning-workflows. 

Als u alle beschikbare gegevenssets wilt bekijken, gaat u naar de [Azure Open Datasets-catalogus](https://azure.microsoft.com/services/open-datasets/catalog/).

Gegevenswetenschappers besteden vaak veel tijd aan gegevensopschoning en -voorbereiding voor geavanceerde analyses. Open Datasets worden gekopieerd naar de Azure-cloud en vooraf verwerkt om u tijd te besparen. Met regelmatige intervallen worden gegevens uit de bronnen opgehaald, bijvoorbeeld via een FTP-verbinding met de National Oceanic and Atmospheric Administration (NOAA). Vervolgens worden gegevens geparseerd in een gestructureerde indeling en naar geschiktheid verrijkt met functies zoals een postcode of locatie van het dichtstbijzijnde weerstation.

Gegevenssets worden meegehost in cloudbereking in Azure, waardoor toegang en manipulatie eenvoudiger is.  

Hieronder staan voorbeelden van beschikbare gegevenssets. 

### <a name="weather-data"></a>Weergegevens
 
|Gegevensset         | Notebooks     | Beschrijving                                    |
|----------------|---------------|------------------------------------------------|
|[NOAA Integrated Surface Data (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Wereldwijde weersgegevens per uur van NOAA met de beste ruimtelijke dekking in Noord-Amerika, Europa, Australië en delen van Azië. Dagelijks bijgewerkt. |
|[NOAA Global Forecast System (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | Elk uur weersvoorspellingsgegevens in de VS van NOA voor de komende 15 dagen. Dagelijks bijgewerkt. |

### <a name="calendar-data"></a>Kalendergegevens

|Gegevensset         | Notebooks     | Beschrijving                                    |
|----------------|---------------|------------------------------------------------|
|[Feestdagen](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Wereldwijde feestdagen met 41 landen of regio's, van 1970 tot 2099. Omvat de landen en of de meeste mensen betaald verlof hebben. |

## <a name="access-to-datasets"></a>Gegevenssets openen  
Met een Azure-account hebt u via code of via de interface van de Azure-service toegang tot Open Datasets. De gegevens worden op dezelfde locatie geplaats als Azure Cloud-rekenresources voor gebruik in uw Machine Learning-oplossing.  

Open Datasets zijn beschikbaar via de gebruikersinterface van Azure Machine Learning en SDK. Open Datasets biedt ook Azure-notebooks en Azure Databricks-notebooks die u kunt gebruiken om gegevens te verbinden met Azure Machine Learning en Azure Databricks. Gegevenssets kunnen ook worden geopend via een Python SDK. 

U hebt echter geen Azure-account nodig om toegang te krijgen tot Open Datasets. U kunt toegang krijgen vanuit elke Python-omgeving, met of zonder Spark.

## <a name="request-or-contribute-datasets"></a>Gegevenssets aanvragen of bijdragen

Als u de gegevens niet kunt vinden die u zoekt, kunt u ons een mail sturen om [een gegevensset aan te vragen](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) of [een gegevensset bij te dragen](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Volgende stappen
* [Voorbeeldnotebook](samples.md)
* [Zelfstudie: Regressiemodellen met taxigegevens uit New York](../machine-learning/tutorial-auto-train-models.md?context=azure%2fopen-datasets%2fcontext%2fopen-datasets-context)
* [Python-SDK voor open gegevenssets](/python/api/azureml-opendatasets/)

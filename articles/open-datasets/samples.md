---
title: Voorbeelden van Jupyter-notebooks met behulp van NOAA-gegevens
titleSuffix: Azure Open Datasets
description: Gebruik voorbeelden van Jupyter-notebooks voor Azure Open Datasets voor meer informatie over het laden van open gegevenssets en gebruik deze om voorbeeldgegevens te verrijken. Voorbeelden van technieken zijn Spark en Pandas voor het verwerken van gegevens.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: 470ed0ea4b129c12041007487b61929843b1de34
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654793"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Voorbeelden van Jupyter-notebooks laten zien hoe gegevens kunnen worden verrijkt met Open Datasets 
De voorbeelden van Jupyter-notebooks voor Azure Open Datasets tonen hoe u open gegevenssets kunt laden en ze gebruiken om voorbeeldgegevens te verrijken. Voorbeelden van technieken zijn Apache Spark en Pandas voor het verwerken van gegevens.

>[!IMPORTANT]
>Als u in een niet-Spark-omgeving werkt, kunt u met Azure Open Datasets slechts gegevens voor één maand tegelijk downloaden met bepaalde klassen om MemoryError met grote gegevenssets te voorkomen.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>NOAA-ISD-gegevens (Integrated Surface Database) laden 
|Notebook        | Beschrijving                                    |
|----------------|------------------------------------------------|
|[Load one recent month of weather data into a Pandas dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) (Eén actuele maand met weergegevens laden in een Pandas-dataframe) | Meer informatie over het laden van historische weergegevens in uw favoriete Pandas-dataframe. |
|[Load one recent month of weather data into a Spark dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) (Eén actuele maand met weergegevens laden in een Spark-dataframe) | Meer informatie over het laden van historische weergegevens in uw favoriete Spark-dataframe.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Voorbeeldgegevens samenvoegen met NOAA-ISD-gegevens 
|Notebook        | Beschrijving                                    |
|----------------|------------------------------------------------|
|[Join demo data with weather data - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) (Voorbeeldgegevens samenvoegen met weergegevens - Pandas) | Voeg een set met voorbeeldgegevens van sensorlocaties gedurende één maand samen met weergegevens in een Panda-dataframe.  |
|[Join demo data with weather data - Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) (Voorbeeldgegevens samenvoegen met weergegevens - Spark) | Voeg een set met voorbeeldgegevens van sensorlocaties samen met weergegevens in een Spark-dataframe. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Gegevens van taxi's in New York samenvoegen met NOAA-ISD-gegevens 
|Notebook        | Beschrijving                                    |
|----------------|------------------------------------------------|
|[Taxi trip data enriched with weather data - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) (Gegevens over taxiritten verrijkt met weergegevens - Pandas) | Laad gegevens van groene taxi's uit New York (meer dan één maand) en verrijk deze met weergegevens in een Panda-dataframe. In dit voorbeeld wordt methode `get_pandas_limit` overschreven en worden de prestaties van gegevensbelasting met de hoeveelheid gegevens uitgebalanceerd.|
|[Taxi trip data enriched with weather data - Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) (Gegevens over taxiritten verrijkt met weergegevens - Spark) | Laad gegevens van groene taxi's uit New York en verrijk deze met weergegevens in een Spark-dataframe.  |

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: regressiemodellen met automatische machine learning en een open gegevensset](../machine-learning/tutorial-auto-train-models.md?context=azure%252fopen-datasets%252fcontext%252fopen-datasets-context)
* [Python-SDK voor open gegevenssets](/python/api/azureml-opendatasets/azureml.opendatasets)
* [Catalogus Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/)
* [Azure Machine Learning-gegevensset maken op basis van open gegevensset](how-to-create-azure-machine-learning-dataset-from-open-dataset.md)

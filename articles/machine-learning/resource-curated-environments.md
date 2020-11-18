---
title: Gecureerde omgevingen
titleSuffix: Azure Machine Learning
description: Meer informatie over Azure Machine Learning gelaagde omgevingen, een reeks vooraf geconfigureerde omgevingen die helpen bij het verminderen van de voorbereidings tijden voor experimenteren en implementaties.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 11/16/2020
ms.openlocfilehash: 0e6817e03c5e5363ad925367b0632e26fc2da4a2
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701395"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learningde gecuratore omgevingen

In dit artikel vindt u een overzicht van de gewerkte omgevingen in Azure Machine Learning en de pakketten en kanalen die vooraf zijn geïnstalleerd. Gehoste omgevingen worden verzorgd door Azure Machine Learning en zijn standaard beschikbaar in uw werk ruimte. Ze worden ondersteund door docker-installatie kopieën in de cache, waardoor de kosten voor het uitvoeren van de voor bereiding worden verminderd en sneller worden geïmplementeerd. Gebruik deze omgevingen om snel aan de slag te gaan met diverse machine learning frameworks.

> [!NOTE]
> Deze lijst is bijgewerkt vanaf september 2020. Gebruik de python-SDK om de meest recente lijst op te halen. Zie het [artikel omgevingen](./how-to-use-environments.md#use-a-curated-environment)voor meer informatie.

## <a name="azureml-automl"></a>AzureML-AutoML

**Pakket kanalen:**

* Anaconda
* Conda-vervalsing
* pytorch

**Conda pakketten:**

* python
* numpy
* scikit-learn
* Pandas
* py-xgboost
* fbprophet
* reizen
* installatie-en git
* psutil

**PIP-pakketten:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-standaards
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* demijnen-schema
* py-cpuinfo

## <a name="azureml-automl-dnn"></a>AzureML-AutoML-DNN

**Pakket kanalen:**

* Anaconda
* Conda-vervalsing
* pytorch

**Conda pakketten:**

* python
* numpy
* scikit-learn
* Pandas
* py-xgboost
* fbprophet
* reizen
* installatie-en git
* pytorch
* cudatoolkit
* psutil

**PIP-pakketten:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-standaards
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* demijnen-schema
* pytorch-trans formaties
* spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>AzureML-AutoML-DNN-GPU

**Pakket kanalen:**

* Anaconda
* Conda-vervalsing
* pytorch

**Conda pakketten:**

* python
* numpy
* scikit-learn
* Pandas
* fbprophet
* reizen
* installatie-en git
* pytorch
* cudatoolkit
* psutil

**PIP-pakketten:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-standaards
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* demijnen-schema
* horovod
* pytorch-trans formaties
* spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-AutoML-DNN-Vision-GPU

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-gegevensset-runtime
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-standaards
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-contrib-automl-DNN-Vision

## <a name="azureml-automl-gpu"></a>AzureML-AutoML-GPU

**Pakket kanalen:**

* Anaconda
* Conda-vervalsing
* pytorch

**Conda pakketten:**

* python
* numpy
* scikit-learn
* Pandas
* fbprophet
* reizen
* installatie-en git
* psutil

**PIP-pakketten:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-standaards
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* demijnen-schema
* py-cpuinfo

## <a name="azureml-chainer-510-cpu"></a>AzureML-Chainer-5.1.0-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* Chainer
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-Chainer-5.1.0-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* Chainer
* cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-Dask-CPU

**Pakket kanalen:**

* Conda-vervalsing
* pytorch
* standaardwaarden

**Conda pakketten:**

* python

**PIP-pakketten:**

* adlfs
* azureml-core
* azureml-gegevensset-runtime
* dask [volt ooien]
* dask-ml [complete]
* gedistribueerde
* fastparquet
* fsspec
* joblib
* jjupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-Dask-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python
* matplotlib

**PIP-pakketten:**

* azureml-standaards
* adlfs
* azureml-core
* dask [volt ooien]
* dask-ml [complete]
* gedistribueerde
* fastparquet
* fsspec
* joblib
* jjupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-Hyperdrive-ForecastDNN

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-standaards
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-contrib-automl-DNN-Forecasting

## <a name="azureml-minimal"></a>AzureML-Minimal

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards

## <a name="azureml-pyspark-mmlspark-015"></a>AzureML-PySpark-MmlSpark-0,15

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>AzureML-PyTorch-1,0-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-10-gpu"></a>AzureML-PyTorch-1,0-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch-1,1-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* toekomstig

## <a name="azureml-pytorch-11-gpu"></a>AzureML-PyTorch-1,1-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* toekomstig

## <a name="azureml-pytorch-12-cpu"></a>AzureML-PyTorch-1,2-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* toekomstig

## <a name="azureml-pytorch-12-gpu"></a>AzureML-PyTorch-1,2-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* toekomstig

## <a name="azureml-pytorch-13-cpu"></a>AzureML-PyTorch-1,3-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* toekomstig

## <a name="azureml-pytorch-13-gpu"></a>AzureML-PyTorch-1,3-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* toekomstig

## <a name="azureml-pytorch-14-cpu"></a>AzureML-PyTorch-1,4-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* toekomstig

## <a name="azureml-pytorch-14-gpu"></a>AzureML-PyTorch-1,4-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* toekomstig

## <a name="azureml-pytorch-15-cpu"></a>AzureML-PyTorch-1,5-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* toekomstig

## <a name="azureml-pytorch-15-gpu"></a>AzureML-PyTorch-1,5-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* toekomstig

## <a name="azureml-pytorch-16-cpu"></a>AzureML-PyTorch-1,6-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* toekomstig

## <a name="azureml-pytorch-16-gpu"></a>AzureML-PyTorch-1,6-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* toekomstig

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-Learn-0.20.3

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>AzureML-tensor flow-1,10-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* tensor flow
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>AzureML-tensor flow-1,10-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* tensor flow-GPU
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>AzureML-tensor flow-1,12-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* tensor flow
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>AzureML-tensor flow-1,12-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* tensor flow-GPU
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>AzureML-tensor flow-1,13-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* tensor flow
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>AzureML-tensor flow-1,13-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* tensor flow-GPU
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>AzureML-tensor Flow-2,0-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* tensor flow
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>AzureML-tensor Flow-2,0-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* tensor flow-GPU
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>AzureML-tensor Flow-2,1-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* tensor flow
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>AzureML-tensor Flow-2,1-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* tensor flow-GPU
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>AzureML-tensor Flow-2,2-CPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* tensor flow
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>AzureML-tensor Flow-2,2-GPU

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* tensor flow-GPU
* horovod

## <a name="azureml-tutorial"></a>AzureML-Tutorial

**Pakket kanalen:**

* Anaconda
* Conda-vervalsing

**Conda pakketten:**

* python
* Pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-telemetry
* azureml-Train-restclients-Hyperdrive
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-trainen
* azureml-SDK
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* mlflow
* sklearn: Pandas

## <a name="azureml-vowpalwabbit-880"></a>AzureML-VowpalWabbit-8.8.0

**Pakket kanalen:**

* Conda-vervalsing

**Conda pakketten:**

* python

**PIP-pakketten:**

* azureml-core
* azureml-standaards
* azureml-gegevensset-runtime [zekering, Pandas]

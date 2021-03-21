---
title: Diep gaande leren & AI-Frameworks
titleSuffix: Azure Data Science Virtual Machine
description: Beschik bare uitgebreide lessen en hulpprogram ma's voor Azure Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 0bfdea6c0bbbe40e4c72ab9da70a1b16d4bc4d3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175665"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Diep gaande lessen en AI-frameworks voor Azure Data Science VM
Uitgebreide lessen op het DSVM worden hieronder weer gegeven.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | |
| Ondersteunde DSVM-edities      | Ubuntu 16.04    |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | Caffe is geïnstalleerd in `/opt/caffe` .   Voor beelden zijn in `/opt/caffe/examples` .|
| Het uitvoeren van het programma      | gebruik X2Go om u aan te melden bij uw virtuele machine, start een nieuwe terminal en voer het volgende in:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Er wordt een nieuw browser venster geopend met voorbeeld notitieblokken. Binaire bestanden worden geïnstalleerd in/opt/Caffe/build/install/bin.<br/><br/>De geïnstalleerde versie van Caffe vereist python 2,7 en werkt niet met python 3,5, die standaard wordt geactiveerd. Als u wilt overschakelen naar python 2,7, voert `source activate root` u uit om over te scha kelen naar de Anaconda-omgeving.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | |
| Ondersteunde DSVM-edities      | Ubuntu 16.04     |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | Caffe2 wordt in de Conda-omgeving [python 2,7 (root) geïnstalleerd. |
| Het uitvoeren van het programma      | Terminal: start python en importeer Caffe2. <br/> * JupyterHub: [Maak verbinding met JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en ga vervolgens naar de map Caffe2 om voorbeeld notitieblokken te vinden. Voor sommige notebooks moet de hoofdmap van het Caffe2 worden ingesteld in de python-code. /opt/caffe2. invoeren |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | 5.2 |
| Ondersteunde DSVM-edities      | Ubuntu 16.04    |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | Chainer is geïnstalleerd in Python 3,5. |
| Het uitvoeren van het programma      | Terminal: Activeer de python 3,5-omgeving, voer uit `python` en klik vervolgens `import chainer` . <br/> * JupyterHub: [Maak verbinding met JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en ga vervolgens naar de map Chainer om voorbeeld notitieblokken te vinden.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA-, cuDNN-, NVIDIA-stuurprogramma](https://developer.nvidia.com/cuda-toolkit)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | 10.0.130|
| Ondersteunde DSVM-edities      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04  |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  |_NVIDIA-SMI_ is beschikbaar op het systeempad.  |
| Het uitvoeren van het programma      | Open een opdracht prompt (in Windows) of een Terminal (op Linux) en voer _NVIDIA-SMI_ uit. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | 0.16.1|
| Ondersteunde DSVM-edities      | Ubuntu 18.04<br> Ubuntu 16.04   |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | Horovod is geïnstalleerd in Python 3,5 |
| Het uitvoeren van het programma      | Activeer de juiste omgeving bij de Terminal en voer python uit. |

## <a name="keras"></a>[Keras](https://keras.io/)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | 2.2.4 |
| Ondersteunde DSVM-edities      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04   |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | Keras is geïnstalleerd in Python 3,6 in Windows en in Python 3,5 in Linux |
| Het uitvoeren van het programma      | Activeer de juiste omgeving bij de Terminal en voer python uit. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](/cognitive-toolkit/)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | 2.5.1 |
| Ondersteunde DSVM-edities      | Windows 2016 <br> Ubuntu 16.04   |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | CNTK is geïnstalleerd in Python 3,6 op [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) en in Python 3,5 op [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Het uitvoeren van het programma      | Terminal: Activeer de juiste omgeving en voer python uit. <br/>Jupyter: Maak verbinding met [Jupyter](provision-vm.md) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open vervolgens de map CNTK voor voor beelden. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | 1.3.0 |
| Ondersteunde DSVM-edities      | Windows 2016 <br> Ubuntu 16.04    |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | MXNet is geïnstalleerd in `C:\dsvm\tools\mxnet` op Windows en `/dsvm/tools/mxnet` op Ubuntu. Python-bindingen worden geïnstalleerd in Python 3,6 op [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) en in Python 3,5 op [Linux](./dsvm-tools-languages.md#python-linux-edition)) R-bindingen zijn ook opgenomen in de Ubuntu-DSVM. |
| Het uitvoeren van het programma      | Terminal: Activeer de juiste Conda-omgeving en voer vervolgens uit `import mxnet` . <br/>Jupyter: Maak verbinding met [Jupyter](provision-vm.md#access-the-dsvm) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open de `mxnet` map voor voor beelden. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | 1.0.1 |
| Ondersteunde DSVM-edities      | Windows 2016 <br> Ubuntu 16.04    |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | MXNet model server is geïnstalleerd in Python 3,6 op [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) en in Python 3,5 op [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Het uitvoeren van het programma      | Terminal: Voer uit `sudo systemctl stop jupyterhub` om de JupyterHub-service eerst te stoppen, omdat beide luistert op dezelfde poort. Activeer vervolgens de juiste Conda-omgeving en voer uit `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia-systeem beheer interface (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies |  |
| Ondersteunde DSVM-edities      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04   |
| Wat is het voor? | NVIDIA-hulp programma voor het uitvoeren van een query op GPU-activiteit |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | `nvidia-smi` bevindt zich op het systeempad. |
| Het uitvoeren van het programma      | Open op een virtuele machine **met GPU** een opdracht prompt (in Windows) of een Terminal (op Linux) en voer deze uit `nvidia-smi` . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | 1.2.0 (Ubuntu 16,04), 1.7.1 (Ubuntu 18,04, Windows 2019) |
| Ondersteunde DSVM-edities      | Windows Server 2019<br>Ubuntu 18.04<br> Ubuntu 16.04 |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | Geïnstalleerd in [Python 3,7](dsvm-tools-languages.md#python-linux-edition) onder de Conda-omgeving van py37_pytorch. Voor beelden van Jupyter-notebooks zijn opgenomen. |
| Het uitvoeren van het programma      | Terminal: Activeer de juiste omgeving en voer python uit.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Maak verbinding en open vervolgens de map PyTorch voor voor beelden.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | 1.13 |
| Ondersteunde DSVM-edities      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04 |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | Geïnstalleerd in Python 3,5 onder [Linux](dsvm-tools-languages.md#python-linux-edition) en python 3,6 op [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Het uitvoeren van het programma      | Terminal: Activeer de juiste omgeving en voer python uit. <br/> * Jupyter: Maak verbinding met [Jupyter](provision-vm.md) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open vervolgens de map tensor flow voor voor beelden.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | 1.12 |
| Ondersteunde DSVM-edities      | Ubuntu 16.04 |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  | tensorflow_model_server is beschikbaar op de Terminal. |
| Het uitvoeren van het programma      |  Voor beelden zijn [online](https://www.tensorflow.org/serving/)beschikbaar.   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

| Categorie | Waarde |
| ------------- | ------------- |
| Ondersteunde versies | 1.0.3 |
| Ondersteunde DSVM-edities      | Ubuntu 16.04 |
| Hoe wordt het geconfigureerd/geïnstalleerd op de DSVM?  |Theano is geïnstalleerd in Python 2,7 (_root_) en in de _py35_-omgeving (python 3,5). |
| Het uitvoeren van het programma      |  Terminal: Activeer de python-versie die u wilt (root of py35), voer python uit en importeer Theano.<br/>* Jupyter: Selecteer de python 2,7-of 3,5-kernel en importeer vervolgens Theano.  <br/>Als u een recente MKL-fout (math kernel Library) wilt omzeilen, moet u eerst de MKL-threading Layer als volgt instellen:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |

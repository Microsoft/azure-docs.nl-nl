---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: 6e7580abfd113a279e2223b9cc9665f6a0b86bc8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562996"
---
1. Gebruik de instructies op [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?view=azure-ml-py) om de Azure Machine Learning SDK voor Python te installeren

1. Een [Azure Machine Learning-werkruimte](../articles/machine-learning/how-to-manage-workspace.md) maken.

1. Een [configuratiebestand](../articles/machine-learning/how-to-configure-environment.md#workspace) schrijven (**aml_config/config.json**).

1. Kloon [de GitHub-opslagplaats](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Start de notebookserver vanuit de gekloonde map.

    ```bash
    jupyter notebook
    ```
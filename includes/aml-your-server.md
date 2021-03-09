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
ms.openlocfilehash: 0d7622217d192a100fd809c32c9e85970cf61fd7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510944"
---
1. Gebruik de instructies op [Azure Machine Learning SDK](/python/api/overview/azure/ml/install) om de Azure Machine Learning SDK voor Python te installeren

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
---
title: Data Science-omgevingen instellen in azure-team data Science process
description: Stel data Science-omgevingen in op Azure voor gebruik in het team data Science process.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 557550627fe2c39571a848723e5d716324fee240
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321164"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Gegevenswetenschapomgevingen instellen voor gebruik in Team Data Science Process
In het proces van het team data Science worden diverse data Science-omgevingen gebruikt voor de opslag, verwerking en analyse van gegevens. Dit zijn onder andere Azure Blob Storage, verschillende typen Azure virtual machines-, HDInsight-clusters (Hadoop) en Azure Machine Learning-werk ruimten. De beslissing over welke omgeving moet worden gebruikt, is afhankelijk van het type en de hoeveelheid gegevens die moeten worden gemodelleerd en de doel bestemming voor die gegevens in de Cloud. 

* Zie [uw Azure machine learning data Science Environment plannen](plan-your-environment.md)voor meer informatie over vragen over het nemen van deze beslissing. 
* Zie [scenario's voor het team data Science process](plan-sample-scenarios.md) voor een catalogus met een aantal scenario's die u kunt tegen komen bij het uitvoeren van geavanceerde analyses.

In de volgende artikelen wordt beschreven hoe u de diverse data Science-omgevingen instelt die worden gebruikt door het team data Science process.

* [Azure-opslag account](../../storage/common/storage-account-create.md)
* [HDInsight-cluster (Hadoop)](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
* [Azure Machine Learning Studio (klassieke) werk ruimte](../classic/create-workspace.md)

**Micro soft data Science virtual machine (DSVM)** is ook beschikbaar als een installatie kopie van een virtuele machine van Azure (VM). Deze VM is vooraf geïnstalleerd en geconfigureerd met verschillende populaire hulpprogram ma's die vaak worden gebruikt voor gegevens analyse en machine learning. De DSVM is beschikbaar op zowel Windows als Linux. Zie [Introduction to the cloud data Science virtual machine voor Linux en Windows](../data-science-virtual-machine/overview.md)voor meer informatie.

Meer informatie over het maken van:

- [Windows-DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu-DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS-DSVM](../data-science-virtual-machine/release-notes.md)
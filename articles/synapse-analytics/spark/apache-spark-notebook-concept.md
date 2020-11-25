---
title: Overzicht van Azure Synapse Analytics-notebooks
description: Dit artikel bevat een overzicht van de mogelijkheden die beschikbaar zijn via Azure Synapse Analytics-notebooks.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4387aadd70ddb373df1bdfa61cbe9ed7f2af283d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919720"
---
# <a name="azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics-notebooks
Een Synapse Studio-Notebook (preview-versie) is een webinterface waarmee u bestanden kunt maken die live code, visualisaties en tekst verhalen bevatten. Notebooks zijn een goede plaats om ideeën te valideren en snelle experimenten te gebruiken om inzicht te krijgen in uw gegevens. 

Met een Azure Synapse Studio-notebook kunt u het volgende doen:

* Ga aan de slag met de installatie van nul.
* Beveilig gegevens met ingebouwde beveiligings functies van het bedrijf.
* Gegevens analyseren over onbewerkte indelingen (CSV, txt, JSON, enzovoort), verwerkte bestands indelingen (Parquet, Delta Lake, ORC, enzovoort) en SQL-gegevens bestanden in tabel vorm tegen Spark en SQL.
* Blijf productief met verbeterde ontwerp mogelijkheden en ingebouwde gegevens visualisatie.

Deze sectie bevat artikelen over het mixen van talen, het maken van gegevens visualisaties, parameterizing-notebooks, het bouwen van pijp lijnen en meer. Het bevat ook verwijzingen en zelf studies over hoe u aan de slag kunt met de ontwikkeling van uw notitie blok.

## <a name="create-manage-and-use-notebooks"></a>Notitie blokken maken, beheren en gebruiken
U kunt notitie blokken beheren met behulp van de Synapse Studio-gebruikers interface. 

Raadpleeg de volgende artikelen voor meer informatie over hoe u notitie blokken kunt maken en beheren:
  - Notitie blokken beheren
    - [Notitie blokken maken](./spark/../apache-spark-development-using-notebooks.md#create-a-notebook)
    - [Notebooks ontwikkelen](./spark/../apache-spark-development-using-notebooks.md#develop-notebooks)
    - [Gegevens naar een notitie blok brengen](./spark/../apache-spark-development-using-notebooks.md#bring-data-to-a-notebook)
    - [Meerdere talen gebruiken met behulp van Magic-opdrachten en tijdelijke tabellen](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)
    - [Cel Magic-opdrachten gebruiken](./spark/../apache-spark-development-using-notebooks.md#magic-commands)
  - Ontwikkeling
    - [Spark-sessie-instellingen configureren](./spark/../apache-spark-development-using-notebooks.md#spark-session-config)
    - [Micro soft Spark-hulpprogram ma's gebruiken](./spark/../microsoft-spark-utilities.md)
    - [Gegevens visualiseren met behulp van notitie blokken en bibliotheken](./spark/../apache-spark-data-visualization.md)
    - [Een notitie blok integreren in pijp lijnen](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)


## <a name="next-steps"></a>Volgende stappen
Notebooks worden ook veel gebruikt in gegevens voorbereiding, gegevens visualisatie, machine learning en andere big data scenario's. Raadpleeg de volgende zelf studies voor meer informatie over hoe u notebooks kunt gebruiken voor uw gegevens analyse en big data scenario's:
  - [Een notebook maken](./spark/../../quickstart-apache-spark-notebook.md)
  - [Visualisaties maken met behulp van Synapse Studio-notebooks](./spark/../apache-spark-data-visualization-tutorial.md)
  - [machine learning modellen bouwen met Apache Spark MLlib](./spark/../apache-spark-machine-learning-mllib-notebook.md)
  - [machine learning modellen bouwen met Azure AutoML](./spark/../apache-spark-azure-machine-learning-tutorial.md)
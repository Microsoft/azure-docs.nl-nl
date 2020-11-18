---
title: 'Zelfstudie: Aan de slag met Azure Synapse Analytics: uw Synapse-werkruimte bewaken'
description: In deze zelfstudie leert u hoe u activiteiten in uw Synapse-werkruimte controleert.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 10/15/2020
ms.openlocfilehash: 2970bb58bb73d52c75729b00a8209a9c576d4ec0
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427408"
---
# <a name="monitor-your-synapse-workspace"></a>Uw Synapse-werkruimte bewaken

In deze zelfstudie leert u hoe u activiteiten in uw Synapse-werkruimte controleert. U kunt de huidige en historische activiteiten voor SQL, Apache Spark en pijplijnen controleren. 

## <a name="introduction-to-the-monitor-hub"></a>Kennismaking met de monitorhub

Ga in Synapse Studio naar de **monitorhub**. Hier ziet u een geschiedenis van alle activiteiten die worden uitgevoerd in de werkruimte en welke nu actief zijn. 

* Onder **Integratie** kunt u pijplijnen, triggers en integratieruntimes bewaken
* Onder **Activiteiten** kunt u Spark- en SQL-activiteiten bewaken. 

## <a name="integration"></a>Integratie

1. Ga naar **Integratie > Pijplijn**. In deze weergave kunt u zien wanneer een pijplijn in uw werkruimte is uitgevoerd. 
1. Zoek de pijplijn die u in de vorige stap hebt uitgevoerd en klik op de **naam van de pijplijn** om meer informatie weer te geven.
1. Klik op **'breadcrumb'-balk** aan de bovenkant van Synapse Studio op **All pipeline runs** (Alle pijplijnuitvoeringen) om terug te keren naar de vorige weergave.

## <a name="apache-spark-activities"></a>Apache Spark-activiteiten

1. Ga naar **Integratie > Activiteiten > Apache Spark-toepassingen**. Nu ziet u alle Spark-toepassingen die worden uitgevoerd of zijn uitgevoerd in uw werkruimte.
1. Zoek een toepassing die niet meer wordt uitgevoerd en klik op de **toepassingsnaam**. Nu ziet u de details van de Spark-toepassing.
1. Als u bekend bent met Apache Spark, kunt u de standaardgebruikersinterface van Apache Spark History Server vinden door te klikken op **Spark History Server**.

## <a name="sql-activities"></a>SQL-activiteiten

1. Ga naar **Integratie > Activiteiten > SQL-aanvragen**.
1. In deze weergave ziet u SQL-aanvragen.
1. Selecteer de **Pool** die u wilt bewaken. Nu ziet u alle SQL-aanvragen die worden uitgevoerd of zijn uitgevoerd in uw werkruimte in die pool.
1. Zoek een specifieke SQL-aanvraag en wijs het desbetreffende item aan met de muis. Zodra u de muisaanwijzer op het item hebt geplaatst, wordt er een SQL-scriptpictogram weergegeven.
1. Klik op het pictogram SQL-script om de volledige tekst van de SQL-aanvraag te bekijken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Het kenniscentrum verkennen](get-started-knowledge-center.md)

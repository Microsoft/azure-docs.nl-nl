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
ms.date: 12/31/2020
ms.openlocfilehash: 7e8525dbebb42e1f387ee8f0c192efd5e64c9453
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426037"
---
# <a name="monitor-your-synapse-workspace"></a>Uw Synapse-werkruimte bewaken

In deze zelfstudie leert u hoe u activiteiten in uw Synapse-werkruimte controleert. U kunt de huidige en historische activiteiten voor SQL, Apache Spark en pijplijnen controleren. 

## <a name="introduction-to-the-monitor-hub"></a>Kennismaking met de monitorhub

Ga in Synapse Studio naar de **monitorhub**. Hier ziet u een geschiedenis van alle activiteiten die worden uitgevoerd in de werkruimte en welke nu actief zijn. 

* Onder **integratie** kunt u pijp lijnen, triggers en Integration Runtimes bewaken.
* Onder **activiteiten** kunt u Spark-en SQL-activiteiten bewaken. 

## <a name="integration"></a>Integratie

1. Navigeer naar **integratie > pijplijn uitvoeringen**. In deze weergave kunt u zien wanneer een pijplijn in uw werkruimte is uitgevoerd. 
1. Zoek de pijplijn die u in de vorige stap hebt uitgevoerd en klik op de **naam van de pijplijn** om meer informatie weer te geven.
1. Klik op **'breadcrumb'-balk** aan de bovenkant van Synapse Studio op **All pipeline runs** (Alle pijplijnuitvoeringen) om terug te keren naar de vorige weergave.

## <a name="apache-spark-activities"></a>Apache Spark-activiteiten

1. Ga naar **activiteiten > Apache Spark toepassingen**. Nu ziet u alle Spark-toepassingen die worden uitgevoerd of zijn uitgevoerd in uw werkruimte.
1. Zoek een toepassing die niet meer wordt uitgevoerd en klik op de **toepassingsnaam**. Nu ziet u de details van de Spark-toepassing.
1. Als u bekend bent met Apache Spark, kunt u de standaardgebruikersinterface van Apache Spark History Server vinden door te klikken op **Spark History Server**.

## <a name="sql-activities"></a>SQL-activiteiten

1. Ga naar **activiteiten > SQL-aanvragen**.
1. In deze weergave ziet u SQL-aanvragen.
1. Selecteer een **pool** die u wilt bewaken vanuit het **groeps** filter. Nu ziet u alle SQL-aanvragen die worden uitgevoerd of zijn uitgevoerd in uw werkruimte in die pool.
1. Zoek een specifieke SQL-aanvraag en klik op de koppeling **meer** om de volledige tekst van de SQL-aanvraag te bekijken.

    > [!NOTE] 
    > SQL-aanvragen die worden ingediend via Synapse Studio in een toegewezen SQL-pool (voorheen SQL DW) met ondersteuning voor werkruimten, kunnen worden weergegeven in de Monitor-hub. Voor alle andere controleactiviteiten kunt u de controlefunctie voor toegewezen SQL-pools (voorheen SQL DW) van de Azure-portal gebruiken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Het kenniscentrum verkennen](get-started-knowledge-center.md)

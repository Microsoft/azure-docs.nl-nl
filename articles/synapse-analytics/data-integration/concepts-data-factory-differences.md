---
title: Verschillen met Azure Data Factory
description: Meer informatie over hoe de mogelijkheden voor gegevens integratie van Azure Synapse Analytics verschillen van die van Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: 9cd3fc9353c684ec6617761616b958477ca83ee3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183638"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Gegevens integratie in azure Synapse Analytics versus Azure Data Factory

In azure Synapse Analytics zijn de mogelijkheden voor gegevens integratie, zoals Synapse-pijp lijnen en gegevens stromen, gebaseerd op die van Azure Data Factory. Zie [Wat is Azure Data Factory](../../data-factory/introduction.md)voor meer informatie.


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Beschik bare functies in ADF & Azure Synapse Analytics

Raadpleeg de onderstaande tabel voor Beschik baarheid van functies:

| Categorie                 | Functie    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | SSIS-en SSIS-Integration Runtime gebruiken | ✓ | ✗ |
|                          | Ondersteuning voor Integration Runtime van meerdere regio's (gegevens stromen) | ✓ | ✗ |
|                          | Integration Runtime delen | ✓<br><small>*Kan worden gedeeld tussen verschillende gegevens fabrieken* | ✗ |
|                          | Time to Live | ✓ | ✗ |
| **Pijp lijnen activiteiten** | SSIS-pakket activiteit | ✓ | ✗ |
|                          | Ondersteuning voor Power Query activiteiten | ✓ | ✓ |
| **Sjabloon galerie en kennis centrum** | Oplossingssjablonen | ✓<br><small>*Galerie met Azure Data Factory sjablonen* | ✓<br><small>*Synapse-werkruimte kennis centrum* |
| **Integratie van GIT-opslag plaats** | GIT-integratie | ✓ | ✓ |
| **Controle**           | Bewaking van Spark-taken voor de gegevens stroom | ✗ | ✓<br><small>*Gebruik de Synapse Spark-Pools* |
|                          | Integratie met Azure Monitor | ✓ | ✗ |
| **Herkomst** | Biedt ondersteuning voor het publiceren van pijplijn afkomst gegevens naar controle sfeer liggen  | ✓ | ✗ |  

> [!Note]
> **Time to Live** is een Azure Integration runtime instelling die ervoor zorgt dat het Spark-cluster gedurende een bepaalde tijd gedurende een periode na een uitvoering van de gegevens stroom *warme blijft* .
>


## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met gegevens integratie in uw Synapse-werk ruimte door te leren hoe u gegevens opneemt [in een Azure data Lake Storage Gen2-account](data-integration-data-lake.md).

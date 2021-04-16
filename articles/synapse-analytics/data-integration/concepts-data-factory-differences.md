---
title: Verschillen met Azure Data Factory
description: Ontdek hoe de mogelijkheden voor gegevensintegratie van Azure Synapse Analytics verschillen van die van Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: 144bdf5e94f753090dd73e5839b6c1fd25f11811
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567635"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Gegevensintegratie in Azure Synapse Analytics versus Azure Data Factory

In Azure Synapse Analytics zijn de mogelijkheden voor gegevensintegratie, zoals Synapse-pijplijnen en gegevensstromen, gebaseerd op die van Azure Data Factory. Zie wat is Azure Data Factory voor [meer informatie.](../../data-factory/introduction.md)


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Beschikbare functies in ADF-& Azure Synapse Analytics

Controleer de onderstaande tabel op beschikbaarheid van functies:

| Categorie                 | Functie    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | SSIS en SSIS Integration Runtime | ✓ | ✗ |
|                          | Ondersteuning voor regio-overschrijdende Integration Runtime (gegevensstromen) | ✓ | ✗ |
|                          | Integration Runtime delen | ✓<br><small>*Kan worden gedeeld tussen verschillende gegevensfabrieken* | ✗ |
|                          | Time to Live | ✓ | ✗ |
| **Pijplijnenactiviteiten** | Activiteit van SSIS-pakket | ✓ | ✗ |
|                          | Ondersteuning voor Power Query activiteit | ✓ | ✓ |
| **Sjabloongalerie en Knowledge Center** | Oplossingssjablonen | ✓<br><small>*Azure Data Factory sjabloongalerie* | ✓<br><small>*Synapse Workspace Knowledge Center* |
| **Integratie van GIT-opslagplaats** | GIT-integratie | ✓ | ✓ |
| **Controle**           | Bewaking van Spark-taken voor Gegevensstroom | ✗ | ✓<br><small>*De Synapse Spark-pools gebruiken* |
|                          | Integratie met Azure Monitor | ✓ | ✗ |
| **Herkomst** | Ondersteunt het publiceren van pijplijngegevens naar Purview  | ✓ | ✗ |  

> [!Note]
> **Time to Live** is een Azure Integration Runtime waarmee het Spark-cluster een  bepaalde tijd warm kan blijven na het uitvoeren van de gegevensstroom.
>


## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met gegevensintegratie in uw Synapse-werkruimte door te leren hoe u gegevens op kunt nemen [in een Azure Data Lake Storage Gen2-account.](data-integration-data-lake.md)

---
title: Tolerantie & hoge Beschik baarheid
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uw Azure Machine Learning-resources flexibeler maakt voor storingen met behulp van een configuratie met een hoge Beschik baarheid.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/16/2020
ms.openlocfilehash: 7a1a63893e6e2988fc5f21e84f21c74315d856b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93325487"
---
# <a name="increase-azure-machine-learning-resiliency"></a>Azure Machine Learning tolerantie verg Roten



In dit artikel leert u hoe u uw Microsoft Azure Machine Learning-resources kunt gebruiken met behulp van configuraties met een hoge Beschik baarheid. U kunt de Azure-Services configureren waarvan Azure Machine Learning afhankelijk is voor hoge Beschik baarheid. In dit artikel worden de services geïdentificeerd die u kunt configureren voor maximale Beschik baarheid en koppelingen naar aanvullende informatie over het configureren van deze resources.

> [!NOTE]
> Azure Machine Learning zelf biedt geen nood herstel optie.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Meer informatie over Azure-Services voor Azure Machine Learning

Azure Machine Learning is afhankelijk van meerdere Azure-Services en heeft meerdere lagen. Sommige van deze services zijn ingericht in uw (klant) abonnement. U bent verantwoordelijk voor de configuratie met hoge Beschik baarheid van deze services. Andere services worden gemaakt in een micro soft-abonnement en worden beheerd door micro soft. 

Azure-Services omvatten:

* **Azure machine learning-infra structuur**: een door micro soft beheerde omgeving voor de Azure machine learning-werk ruimte.

* **Gekoppelde resources**: resources die in uw abonnement zijn ingericht tijdens het maken van Azure machine learning werk ruimte. Deze resources omvatten Azure Storage, Azure Key Vault, Azure Container Registry en Application Insights. U bent zelf verantwoordelijk voor het configureren van instellingen voor hoge Beschik baarheid voor deze resources.
  * De standaard opslag bevat gegevens, zoals model, logboek gegevens van de training en de gegevensset.
  * Key Vault heeft referenties voor Azure Storage, Container Registry en gegevens archieven.
  * Container Registry beschikt over een docker-installatie kopie voor trainings-en detraining omgevingen.
  * Application Insights is voor het bewaken van Azure Machine Learning.

* **Reken resources**: resources die u maakt na de implementatie van de werk ruimte. U kunt bijvoorbeeld een reken instantie of reken cluster maken om een Machine Learning model te trainen.
  * Reken instantie en reken cluster: micro soft Managed model ontwikkel omgevingen.
  * Andere bronnen: Micro Soft computing resources die u kunt koppelen aan Azure Machine Learning, zoals Azure Kubernetes service (AKS), Azure Databricks, Azure Container Instances en Azure HDInsight. U bent zelf verantwoordelijk voor het configureren van instellingen voor hoge Beschik baarheid voor deze resources.

* **Aanvullende gegevens archieven**: Azure machine learning kunt extra gegevens archieven zoals Azure Storage, Azure Data Lake Storage en Azure SQL database koppelen voor trainings gegevens.  Deze gegevens archieven worden ingericht binnen uw abonnement. U bent zelf verantwoordelijk voor het configureren van de instellingen voor hoge Beschik baarheid.

In de volgende tabel ziet u welke Azure-Services worden beheerd door micro soft, die door u worden beheerd en die standaard Maxi maal beschikbaar zijn.

| Service | Beheerd door | Standaard hoge Beschik baarheid |
| ----- | ----- | ----- |
| **Azure Machine Learning-infra structuur** | Microsoft | |
| **Gekoppelde resources** |
| Azure Storage | U | |
| Key Vault | U | ✓ |
| Container Registry | U | |
| Application Insights | U | NA |
| **Reken resources** |
| Rekenproces | Microsoft |  |
| Rekencluster | Microsoft |  |
| Andere reken resources, zoals AKS, <br>Azure Databricks, Container Instances, HDInsight | U |  |
| **Aanvullende gegevens archieven** zoals Azure Storage, SQL database,<br> Azure Database for PostgreSQL, Azure Database for MySQL, <br>Azure Databricks bestands systeem | U | |

In de rest van dit artikel worden de acties beschreven die u moet uitvoeren om elk van deze services Maxi maal beschikbaar te maken.

## <a name="associated-resources"></a>Gekoppelde resources

> [!IMPORTANT]
> Azure Machine Learning biedt geen ondersteuning voor de standaard failover van een opslag account met behulp van geo-redundante opslag (GRS), geo-zone-redundante opslag (GZRS), geografisch redundante opslag met lees toegang (RA-GRS) of geo-zone-redundante opslag met lees toegang (RA-GZRS).

Zorg ervoor dat u de instellingen voor hoge Beschik baarheid van elke resource configureert door te verwijzen naar de volgende documentatie:

* **Azure Storage**: Zie [Azure Storage redundantie](../storage/common/storage-redundancy.md)voor het configureren van instellingen voor hoge Beschik baarheid.
* **Key Vault**: Key Vault biedt standaard een hoge Beschik baarheid en vereist geen gebruikers actie.  Zie [Azure Key Vault Beschik baarheid en redundantie](../key-vault/general/disaster-recovery-guidance.md).
* **Container Registry**: Kies de optie Premium Registry voor geo-replicatie. Zie [geo-replicatie in azure container Registry](../container-registry/container-registry-geo-replication.md).
* **Application Insights**: Application Insights biedt geen instellingen voor hoge Beschik baarheid. Zie [gegevens verzameling, retentie en opslag in Application Insights](../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)om de Bewaar periode voor gegevens en Details aan te passen.

## <a name="compute-resources"></a>Rekenresources

Zorg ervoor dat u de instellingen voor hoge Beschik baarheid van elke resource configureert door te verwijzen naar de volgende documentatie:

* **Azure Kubernetes service**: Zie [Aanbevolen procedures voor bedrijfs continuïteit en herstel na nood gevallen in azure KUBERNETES service (AKS)](../aks/operator-best-practices-multi-region.md) en [Maak een Azure KUBERNETES service-cluster (AKS) dat gebruikmaakt van beschikbaarheids zones](../aks/availability-zones.md). Als het AKS-cluster is gemaakt met behulp van de Azure Machine Learning Studio, SDK of CLI, wordt de hoge Beschik baarheid voor meerdere regio's niet ondersteund.
* **Azure Databricks**: Zie [regionale nood herstel voor Azure Databricks clusters](/azure/databricks/scenarios/howto-regional-disaster-recovery).
* **Container instances**: een Orchestrator is verantwoordelijk voor failover. Zie [Azure container instances en container Orchestrator](../container-instances/container-instances-orchestrator-relationship.md).
* **HDInsight**: Bekijk [Services met hoge Beschik baarheid die worden ondersteund door Azure HDInsight](../hdinsight/hdinsight-high-availability-components.md).

## <a name="additional-data-stores"></a>Aanvullende gegevens archieven

Zorg ervoor dat u de instellingen voor hoge Beschik baarheid van elke resource configureert door te verwijzen naar de volgende documentatie:

* **Azure Blob container/Azure files/data Lake Storage Gen2**: hetzelfde als standaard opslag.
* **Data Lake Storage gen1**: Zie de [richt lijnen voor hoge Beschik baarheid en herstel na nood gevallen voor data Lake Storage gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md).
* **SQL database**: Bekijk [hoge Beschik baarheid voor Azure SQL database en SQL Managed instance](../azure-sql/database/high-availability-sla.md).
* **Azure database for PostgreSQL**: Bekijk [concepten met hoge Beschik baarheid in azure database for PostgreSQL-één server](../postgresql/concepts-high-availability.md).
* **Azure database for MySQL**: Zie [inzicht in bedrijfs continuïteit in azure database for MySQL](../mysql/concepts-business-continuity.md).
* **Azure Databricks bestands systeem**: Zie [regionale nood herstel voor Azure Databricks clusters](/azure/databricks/scenarios/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Als u uw eigen door de klant beheerde sleutel voor het implementeren van een Azure Machine Learning-werk ruimte opgeeft, wordt Azure Cosmos DB ook in uw abonnement ingericht. In dat geval bent u verantwoordelijk voor het configureren van de instellingen voor hoge Beschik baarheid. Zie [Hoge beschikbaarheid in Azure Cosmos DB](../cosmos-db/high-availability.md).

## <a name="next-steps"></a>Volgende stappen

Gebruik een [Azure Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced)om Azure machine learning met gekoppelde resources te implementeren met uw instellingen voor hoge Beschik baarheid.
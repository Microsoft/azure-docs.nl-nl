---
title: Inleiding tot versie beheer-Azure HDInsight
description: Meer informatie over hoe versie beheer werkt in azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 6db4c7856ebdf75d5bf94de1e3110bb25bc93e69
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493863"
---
# <a name="how-versioning-works-in-hdinsight"></a>Hoe versie beheer werkt in HDInsight

De HDInsight-service heeft twee hoofd onderdelen: een resource provider en Apache Hadoop onderdelen die in een cluster worden geïmplementeerd. 

## <a name="hdinsight-resource-provider"></a>HDInsight-resource provider

Resources in Azure worden beschikbaar gesteld door een resource provider. De HDInsight-resource provider is verantwoordelijk voor het maken, beheren en verwijderen van clusters.

## <a name="hdinsight-images"></a>HDInsight-installatie kopieën

HDInsight gebruikt installatie kopieën voor het samen stellen van open source software (OSS)-onderdelen die op een cluster kunnen worden geïmplementeerd. Deze installatie kopieën bevatten het basis Ubuntu-besturings systeem en de belangrijkste onderdelen, zoals Spark, Hadoop, Kafka, HBase of Hive.

## <a name="versioning-in-hdinsight"></a>Versie beheer in HDInsight

Micro soft voert regel matig een upgrade uit van de installatie kopieën en de HDInsight-resource provider, zodat nieuwe verbeteringen en functies worden opgenomen.

Nieuwe HDInsight-versie kan worden gemaakt wanneer een of meer van de volgende voor waarden waar zijn:

- Belang rijke wijzigingen of updates voor de functionaliteit van de HDInsight-resource provider
- Grote releases van OSS-onderdelen
- Belang rijke wijzigingen in het Ubuntu-besturings systeem

Er wordt een nieuwe installatie kopie versie gemaakt wanneer een of meer van de volgende voor waarden waar zijn:

- Grote of kleine releases en updates van OSS-onderdelen
- Patches of oplossingen voor een onderdeel in de installatie kopie

## <a name="next-steps"></a>Volgende stappen

- [Apache-onderdelen en versies in HDInsight](./hdinsight-component-versioning.md)

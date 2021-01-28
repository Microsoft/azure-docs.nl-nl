---
title: Ondersteunde knooppunt configuraties van Azure HDInsight
description: Meer informatie over de minimale en aanbevolen configuraties voor HDInsight-cluster knooppunten.
keywords: VM-grootten, cluster grootte, cluster configuratie
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/14/2020
ms.openlocfilehash: d41ee2554d30a56bc2e025bbe2c93aee143d75e8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931655"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Wat zijn de standaard en aanbevolen knooppunt configuraties voor Azure HDInsight?

In dit artikel worden de standaard-en aanbevolen knooppunt configuraties voor Azure HDInsight-clusters besproken.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standaard en mini maal aanbevolen knooppunt configuratie en grootte van virtuele machines voor clusters

In de volgende tabellen worden de standaard en aanbevolen VM-grootten (Virtual Machine) weer geven voor HDInsight-clusters.  Deze informatie is nodig om inzicht te krijgen in de VM-grootten die moeten worden gebruikt bij het maken van Power shell-of Azure CLI-scripts voor het implementeren van HDInsight-clusters.

Als u meer dan 32 worker-knoop punten in een cluster nodig hebt, selecteert u een hoofd knooppunt grootte met ten minste 8 kernen en 14 GB aan RAM-geheugen.

De enige cluster typen met gegevens schijven zijn Kafka-en HBase-clusters waarvoor de functie voor versneld schrijven is ingeschakeld. HDInsight ondersteunt de P30-en S30-schijf grootten in deze scenario's. Voor alle andere cluster typen biedt HDInsight beheerde schijf ruimte met het cluster. Vanaf 11/07/2019 is de beheerde schijf grootte van elk knoop punt in het zojuist gemaakte cluster 128 GB. Dit kan niet worden gewijzigd.

De specificaties van alle mini maal aanbevolen VM-typen die in dit document worden gebruikt, worden in de volgende tabel samenvatten.

| Grootte              | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Maximumaantal gegevensschijven / doorvoer: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12.000 / 187 / 93                                           | 16-16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24.000 / 375 / 187                                          | 32/32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48.000 / 750 / 375                                          | 64/64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12.000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24.000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48.000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |

Zie de volgende documenten voor meer informatie over de specificaties van elk VM-type:

* [Grootte van virtuele machines voor algemene doel einden: dv2 Series 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Grootte van virtuele machines geoptimaliseerd voor geheugen: dv2 Series 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Grootte van virtuele machines voor algemene doel einden: Av2 Series 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Alle ondersteunde regio's met uitzonde ring van Brazilië-Zuid en Japan-West

> [!Note]
> Als u de SKU-id wilt ophalen voor gebruik in Power shell en andere scripts, voegt `Standard_` u aan het begin van alle VM-sku's in de onderstaande tabellen toe. Dit wordt bijvoorbeeld `D12_v2` `Standard_D12_v2` .

| Clustertype | Hadoop | HBase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Head: standaard VM-grootte | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Kop: Mini maal aanbevolen VM-grootten | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Werk nemer: standaard VM-grootte | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 met 2 S30 schijven per Broker |
| Werk nemer: Mini maal aanbevolen VM-grootten | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: standaard VM-grootte |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: Mini maal aanbevolen VM-grootten |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML Services: standaard VM-grootte |  |  |  |  |  | D4_v2 |  |
| ML Services: Mini maal aanbevolen VM-grootte |  |  |  |  |  | D4_v2 |  |

\* = VM-grootten voor Spark-Enterprise Security Package (ESP)-clusters

### <a name="brazil-south-and-japan-west-only"></a>Alleen Brazilië-Zuid en Japan-West

| Clustertype | Hadoop | HBase | Interactive Query | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Head: standaard VM-grootte | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Kop: Mini maal aanbevolen VM-grootten | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Werk nemer: standaard VM-grootte | D4 | D4 | D14 | D3 | D13 | D4 |
| Werk nemer: Mini maal aanbevolen VM-grootten | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: standaard VM-grootte |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: Mini maal aanbevolen VM-grootten |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML Services: standaard VM-grootten |  |  |  |  |  | D4 |
| ML Services: Mini maal aanbevolen VM-grootten |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - Head staat bekend als *Nimbus* voor het Storm-cluster type.
> - Werk nemer staat bekend als *Super Visor* voor het Storm-cluster type.
> - De werk nemer staat bekend als *regio* voor het cluster type HBase.

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn de Apache Hadoop onderdelen en versies die beschikbaar zijn in HDInsight?](hdinsight-component-versioning.md)

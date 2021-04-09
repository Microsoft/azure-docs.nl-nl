---
title: Apache Hadoop onderdelen en versies-Azure HDInsight
description: Meer informatie over de Apache Hadoop onderdelen en versies in azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: dbd5b507fd4a7b2434158dbdc80584a7fd348732
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726580"
---
# <a name="azure-hdinsight-versions"></a>Azure HDInsight-versies

HDInsight bundelt Apache Hadoop omgevings onderdelen en HDInsight-platform in een pakket dat in een cluster wordt geïmplementeerd. Zie [hoe HDInsight versie beheer werkt](hdinsight-overview-versioning.md)voor meer informatie.

## <a name="supported-hdinsight-versions"></a>Ondersteunde HDInsight-versies

Deze tabel geeft een lijst van de versies van HDInsight die beschikbaar zijn in de Azure Portal en andere implementatie methoden, zoals Power shell, CLI en de .NET SDK.

| HDInsight-versie | VM-besturingssysteem | Releasedatum| Ondersteunings type | Verval datum ondersteuning | Buitengebruikstellings datum | Hoge beschikbaarheid |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |September 24, 2018 | [Standard](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |Yes |
| [HDInsight 3,6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4 april 2017      | [Basic](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Verval datum van de standaard ondersteuning van 30 juni 2021 <br> Basic Support verloop: 3 april 2022 |4 april 2022 |Yes |

* Vanaf 1 juli 2021 micro soft biedt Basic Support voor bepaalde cluster typen van HDI 3,6. Zie [versies van HDInsight 3,6-onderdelen](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Opmerkingen bij de release

Zie [release opmerkingen voor hdinsight](hdinsight-release-notes.md)voor aanvullende opmerkingen bij de release over de nieuwste versies van hdinsight.

## <a name="support-options-for-hdinsight-versions"></a>Ondersteunings opties voor HDInsight-versies

Ondersteuning wordt gedefinieerd als een tijds periode waarin een HDInsight-versie wordt ondersteund door de klanten service en ondersteuning van micro soft. HDInsight biedt twee soorten ondersteuning: 
- **Standaard ondersteuning** is een tijds periode waarin micro soft updates en ondersteuning biedt voor HDInsight-clusters.  
    Het is raadzaam om oplossingen te bouwen met de meest recente volledig ondersteunde versie. 
- **Basic Support** is een periode waarin micro soft beperkte onderhoud levert aan de HDInsight-resource provider. HDInsight-installatie kopieën en OSS-onderdelen (open-source software) worden niet onderhouden.   Er worden alleen kritieke beveiligings correcties op HDInsight-clusters geïnstalleerd.  
  Micro soft moedigt het maken van nieuwe clusters niet aan of bouwt geen verse oplossingen op wanneer een versie in Basic Support. Het is raadzaam om bestaande clusters te migreren naar de meest recente volledig ondersteunde versie. 

**Verval** van de ondersteuning houdt in dat micro soft geen ondersteuning meer biedt voor de specifieke HDInsight-versie. En is mogelijk niet meer beschikbaar via de Azure Portal voor het maken van een cluster.

**Buiten** gebruik stellen wordt aangegeven dat bestaande clusters van een HDInsight-versie als zodanig blijven worden uitgevoerd. Nieuwe clusters van deze versie kunnen niet op een wille keurige manier worden gemaakt, met inbegrip van de CLI en Sdk's. Andere functies van het besturings systeem, zoals hand matig schalen en automatisch schalen, zijn niet gegarandeerd na de pensionerings datum. Ondersteuning is niet beschikbaar voor niet-verouderde versies.

## <a name="versioning-considerations"></a>Overwegingen voor versie beheer
- Zodra een cluster is geïmplementeerd met een installatie kopie, wordt dat cluster niet automatisch bijgewerkt naar nieuwere versie van de installatie kopie. Bij het maken van nieuwe clusters wordt de recentste versie van de installatie kopie geïmplementeerd.
- Klanten moeten testen en valideren dat toepassingen goed worden uitgevoerd als er een nieuwe HDInsight-versie wordt gebruikt.
- HDInsight behoudt zich het recht voor om de standaard versie zonder voorafgaande kennisgeving te wijzigen. Als u een versie afhankelijkheid hebt, geeft u de HDInsight-versie op wanneer u uw clusters maakt.
- HDInsight kan een versie van het OSS-onderdeel buiten gebruik stellen voordat de HDInsight-versie wordt gestoten.

## <a name="next-steps"></a>Volgende stappen

- [Setup van het cluster voor Apache Hadoop, Spark en meer op HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Werken in Apache Hadoop op HDInsight vanaf een Windows-computer](hdinsight-hadoop-windows-tools.md)

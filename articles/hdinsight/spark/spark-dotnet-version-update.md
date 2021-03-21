---
title: .NET voor Apache Spark bijwerken in versie v 1.0 in HDI
description: Meer informatie over het bijwerken van .NET voor Apache Spark versie naar 1,0 in HDI en over de manier waarop uw bestaande code en clusters worden beïnvloed.
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788126"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>.NET voor Apache Spark bijwerken naar versie v 1.0 in HDInsight

In dit document vindt u informatie over de eerste primaire versie van [.net voor Apache Spark](https://github.com/dotnet/spark)en hoe dit kan van invloed zijn op uw huidige productie pijplijnen in HDInsight-clusters.

## <a name="about-net-for-apache-spark-version-100"></a>Over .NET voor Apache Spark versie 1.0.0

Dit is de eerste [belang rijke officiële versie](https://github.com/dotnet/spark/releases/tag/v1.0.0) van .net voor Apache Spark en biedt data frame API-volledigheid voor Spark 2.4. x, en Spark 3.0. x en andere functies. Zie de release opmerkingen voor de officiële [v 1.0.0](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md)voor een volledige lijst met alle functies, verbeteringen en oplossingen voor problemen.
Een andere belang rijke ding is dat deze versie **niet** compatibel is met eerdere versies van `Microsoft.Spark` en `Microsoft.Spark.Worker` . Raadpleeg de [migratie handleiding](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) als u van plan bent om uw .net for Apache Spark-toepassing te upgraden zodat deze compatibel is met v 1.0.0.

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>.NET gebruiken voor Apache Spark v 1.0 in HDInsight

Hoewel de huidige HDI-clusters niet worden beïnvloed (dat wil zeggen dat ze nog steeds dezelfde versie hebben als voorheen), zal nieuw gemaakte HDI-clusters deze laatste v 1.0.0-versie van .NET voor Apache Spark bevatten. Wat betekent dit als:

- **U hebt een ouder HDI-cluster**: als u uw Spark .NET-toepassing wilt upgraden naar v 1.0.0 (aanbevolen), moet u de versie bijwerken `Microsoft.Spark.Worker` op uw HDI-cluster. Zie de [sectie veranderende versies van .net voor Apache Spark op HDI-cluster](#changing-net-for-apache-spark-version-on-hdinsight)voor meer informatie.
Als u de huidige versie van .NET voor Apache Spark in uw toepassing niet wilt bijwerken, zijn er geen verdere stappen vereist.  

- **U hebt een nieuw HDI-cluster**: als u uw Spark .NET-toepassing wilt upgraden naar v 1.0.0 (aanbevolen), zijn er geen stappen nodig om de werk NEMER op HDI te wijzigen. u moet echter wel de [migratie handleiding](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) raadplegen om inzicht te krijgen in de stappen die nodig zijn om uw code en pijp lijnen bij te werken.
Als u de huidige versie van .NET voor Apache Spark in uw toepassing niet wilt wijzigen, moet u de versie op uw HDI-cluster wijzigen van v 1.0 (standaard op nieuwe clusters) voor de versie die u gebruikt. Zie de [sectie veranderende versies van .net voor Apache Spark op HDI-cluster](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight)voor meer informatie.  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>.NET voor Apache Spark versie op HDInsight wijzigen

### <a name="deploy-microsoftsparkworker"></a>Micro soft. Spark. worker implementeren

`Microsoft.Spark.Worker` is een back-end-onderdeel dat zich op de afzonderlijke werk knooppunten van uw Spark-cluster bevindt. Wanneer u een C# UDF (door de gebruiker gedefinieerde functie) wilt uitvoeren, moet Spark begrijpen hoe de .NET CLR moet worden gestart om deze UDF uit te voeren. `Microsoft.Spark.Worker` voorziet in een verzameling van klassen aan Spark waarmee deze functionaliteit kan worden ingeschakeld. Selecteer de versie van de werk nemer, afhankelijk van de versie van .NET voor Apache Spark u op het HDI-cluster wilt implementeren.

1. Down load de versie van micro soft. Spark. worker Linux voor uw specifieke versie. Als u wilt `.NET for Apache Spark v1.0.0` , kunt u bijvoorbeeld [micro soft. Spark. Worker. netcoreapp 3.1. Linux-x64-1.0.0. tar. gz](https://github.com/dotnet/spark/releases/tag/v1.0.0)downloaden.  

2. Down load het [install-Worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) -script om de binaire bestanden van de werk nemers te installeren die u in stap 1 hebt gedownload naar alle worker-knoop punten van uw HDI  

3. Upload de hierboven vermelde bestanden naar het Azure Storage-account waartoe uw cluster toegang heeft. Raadpleeg [het artikel over de implementatie van .net for Apache Spark HDI](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure) voor meer informatie.

4. Voer het `install-worker.sh` script uit op alle worker-knoop punten van uw cluster met behulp van script acties. Raadpleeg [het artikel over de implementatie van .net for Apache Spark HDI](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action) voor meer informatie.

### <a name="update-your-application-to-use-specific-version"></a>Uw toepassing bijwerken voor het gebruik van een specifieke versie

U kunt uw .NET for Apache Spark-toepassing bijwerken voor gebruik van een specifieke versie door de vereiste versie van het [micro soft. Spark NuGet-pakket](https://www.nuget.org/packages/Microsoft.Spark/) in uw project te kiezen. Bekijk de release opmerkingen van de specifieke versie en de eerder genoemde [migratie handleiding](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) als u ervoor kiest om uw toepassing bij te werken naar v 1.0.0.

## <a name="faqs"></a>Veelgestelde vragen

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>Wordt mijn bestaande HDI-cluster met versie < 1.0.0 starten met de nieuwe versie?

Bestaande HDI-clusters blijven dezelfde vorige versie voor .NET voor Apache Spark en uw bestaande toepassing (met eerdere versie van Spark .NET) wordt niet beïnvloed.

## <a name="next-steps"></a>Volgende stappen

[Uw .NET for Apache Spark-toepassing implementeren in HDInsight](/dotnet/spark/tutorials/hdinsight-deployment)
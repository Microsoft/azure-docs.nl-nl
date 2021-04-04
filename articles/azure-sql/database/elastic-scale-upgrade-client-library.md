---
title: Upgrade uitvoeren naar de nieuwste client bibliotheek voor Elastic data base
description: Gebruik NuGet om de client bibliotheek voor Elastic data base bij te werken.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 74aed815d011503cb6caea56cfad5e076bdcbfbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793412"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Een app bijwerken voor het gebruik van de meest recente Elastic data base-client bibliotheek
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Nieuwe versies van de [Elastic database-client bibliotheek](elastic-database-client-library.md) zijn beschikbaar via NuGet en de NuGet Package Manager-interface in Visual Studio. Upgrades bevatten oplossingen voor fouten en ondersteuning voor nieuwe mogelijkheden van de client bibliotheek.

**Voor de meest recente versie:** Ga naar [micro soft. Azure. SqlDatabase. ElasticScale. client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Bouw uw toepassing opnieuw op met de nieuwe bibliotheek en wijzig uw bestaande meta gegevens voor Shard-toewijzings beheer die zijn opgeslagen in uw data bases in Azure SQL Database ter ondersteuning van nieuwe functies.

Door deze stappen uit te voeren, zorgt u ervoor dat oude versies van de client bibliotheek niet meer aanwezig zijn in uw omgeving wanneer meta gegevens objecten worden bijgewerkt. Dit betekent dat meta gegevens objecten van de oude versie niet worden gemaakt na de upgrade.

## <a name="upgrade-steps"></a>Upgrade stappen

**1. Voer een upgrade uit voor uw toepassingen.** In Visual Studio downloadt en verwijst u naar de meest recente versie van de client bibliotheek in al uw ontwikkelings projecten die gebruikmaken van de bibliotheek. bouw en implementeer vervolgens opnieuw.

* Selecteer in uw Visual Studio-oplossing **extra**  -->  **NuGet package manager**  -->   **NuGet-pakketten beheren voor oplossing**.
* (Visual Studio 2013) Selecteer **updates** in het linkerdeel venster en selecteer vervolgens de knop **bijwerken** op het pakket **Azure SQL database Elastic Scale-client bibliotheek** die wordt weer gegeven in het venster.
* (Visual Studio 2015) Stel het filter in op **upgrade beschikbaar**. Selecteer het pakket dat u wilt bijwerken en klik op de knop **bijwerken** .
* (Visual Studio 2017) Selecteer boven in het dialoog venster **updates**. Selecteer het pakket dat u wilt bijwerken en klik op de knop **bijwerken** .
* Bouwen en implementeren.

**2. Voer een upgrade uit voor uw scripts.** Als u **Power shell** -scripts gebruikt om Shards te beheren, [downloadt u de nieuwe bibliotheek versie](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) en kopieert u deze naar de map van waaruit u scripts uitvoert.

**3. werk uw service voor splitsen en samen voegen bij.** Als u het Elastic data base-hulp programma voor splitsen en samen voegen gebruikt om Shard-gegevens opnieuw in te delen, [downloadt en implementeert u de meest recente versie van het hulp programma](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Gedetailleerde upgrade stappen voor de service vindt u [hier](elastic-scale-overview-split-and-merge.md).

**4. werk de data bases van uw Shard-map beheer** bij. Voer een upgrade uit voor de meta gegevens die uw Shard-kaarten ondersteunen in Azure SQL Database.  U kunt dit op twee manieren doen met behulp van Power shell of C#. Beide opties worden hieronder weer gegeven.

***Optie 1: meta gegevens bijwerken met Power shell***

1. Down load [hier](https://nuget.org/nuget.exe) het meest recente opdracht regel programma voor NuGet en sla het op in een map.
2. Open een opdracht prompt, navigeer naar dezelfde map en geef de volgende opdracht: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Ga naar de submap met de nieuwe client-DLL-versie die u zojuist hebt gedownload, bijvoorbeeld: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Down load het client upgrade script voor Elastic data base uit het [script centrum](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)en sla het op in dezelfde map die de dll bevat.
5. Voer vanuit die map Power shell .\upgrade.ps1 uit vanaf de opdracht prompt en volg de aanwijzingen.

***Optie 2: meta gegevens bijwerken met C #***

U kunt ook een Visual Studio-toepassing maken waarmee uw ShardMapManager wordt geopend, alle Shards worden herhaald en de upgrade van de meta gegevens wordt uitgevoerd door de methoden [UpgradeLocalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) en [UpgradeGlobalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) aan te roepen, zoals in dit voor beeld:

```csharp
    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }
```

Deze technieken voor het bijwerken van meta gegevens kunnen meerdere keren worden toegepast zonder beschadiging. Als bijvoorbeeld een oudere client versie per ongeluk een Shard maakt nadat u al een update hebt uitgevoerd, kunt u de upgrade opnieuw uitvoeren over alle Shards om ervoor te zorgen dat de meest recente versie van de meta gegevens aanwezig is in uw infra structuur.

**Opmerking:**  Nieuwe versies van de client bibliotheek gepubliceerd op datum blijven werken met eerdere versies van de meta gegevens van het Shard-toewijzings beheer op Azure SQL Database en vice versa.   Als u echter wilt profiteren van een aantal nieuwe functies in de meest recente client, moeten meta gegevens worden bijgewerkt.   Houd er rekening mee dat meta gegevens upgrades geen invloed hebben op gebruikers gegevens of toepassingsspecifieke gegevens, alleen objecten die worden gemaakt en gebruikt door Shard-toewijzings beheer.  En toepassingen blijven actief door de hierboven beschreven upgrade volgorde.

## <a name="elastic-database-client-version-history"></a>Geschiedenis van client versie voor Elastic data base

Ga voor versie geschiedenis naar [micro soft. Azure. SqlDatabase. ElasticScale. client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
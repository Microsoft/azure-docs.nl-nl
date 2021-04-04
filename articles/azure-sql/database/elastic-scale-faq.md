---
title: Veelgestelde vragen over Elastic Scale
description: Veelgestelde vragen over Azure SQL Database Elastic scale.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 51e15a8dc5e9f918c630397d6d6593f5bf561755
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92786901"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Veelgestelde vragen over hulpprogram ma's voor Elastic data base
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Hoe kan ik de sharding-sleutel voor de schema-informatie invullen als ik een single-tenant per Shard en geen sharding-sleutel heb?

Het object schema-info wordt alleen gebruikt om samenvoeg scenario's te splitsen. Als een toepassing inherent aan één Tenant is, is het hulp programma voor het samen voegen van splitsen niet vereist en hoeft het object schema gegevens niet te worden ingevuld.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Ik heb een Data Base ingericht en ik heb al een Shard-toewijzings beheer, hoe kan ik deze nieuwe Data Base als een Shard registreren

Zie [een Shard toevoegen aan een toepassing met behulp van de client bibliotheek voor Elastic data base](elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Wat zijn de kosten voor Elastic data base-hulpprogram ma's?

Het gebruik van de client bibliotheek voor Elastic data base maakt geen kosten in rekening. De kosten zijn alleen van toepassing op de data bases in Azure SQL Database die u gebruikt voor Shards en het Shard-toewijzings beheer, evenals de Web/Worker-rollen die u voor het hulp programma voor splitsen samen voegen hebt ingericht.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Waarom werken mijn referenties niet wanneer ik een Shard van een andere server toevoeg

Gebruik geen referenties in de vorm ' gebruikers-ID = username@servername ', in plaats daarvan alleen ' gebruikers-ID = gebruikers naam ' te gebruiken.  Zorg er ook voor dat de aanmelding ' gebruikers naam ' machtigingen heeft voor de Shard.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Moet ik een Shard-toewijzings beheer maken en Shards elke keer invullen wanneer ik mijn toepassingen start

Nee: het maken van het Shard-toewijzings beheer (bijvoorbeeld [ShardMapManagerFactory. CreateSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) is een eenmalige bewerking.  Uw toepassing moet de aanroep [ShardMapManagerFactory. TryGetSqlShardMapManager ()](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) op de start tijd van de toepassing gebruiken.  Er mag slechts één aanroep per toepassings domein zijn.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Ik heb vragen over het gebruik van hulpprogram ma's voor elastische data bases, hoe krijg ik deze antwoorden

Neem contact met ons op de [pagina micro soft Q&een vraag voor SQL database](/answers/topics/azure-sql-database.html).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Wanneer ik een database verbinding krijg met behulp van een sharding-sleutel, kan ik nog steeds gegevens opvragen voor andere sharding-sleutels op dezelfde Shard.  Is dit per ontwerp

De Elastic Scale-Api's bieden u een verbinding met de juiste Data Base voor uw sharding-sleutel, maar bieden geen sharding-sleutel filtering.  Voeg de component **where** toe aan uw query om het bereik te beperken tot de gegeven sharding-sleutel, indien nodig.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Kan ik een andere SQL Database Edition gebruiken voor elke Shard in mijn Shard-set

Ja, een Shard is een afzonderlijke data base en daarom kan één Shard een Premium-editie zijn, terwijl een andere een Standard-editie is. Verder kan de versie van een Shard meerdere keren omhoog of omlaag worden geschaald tijdens de levens duur van de Shard.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Voorziet het samen voegen van een Data Base tijdens het splitsen (of verwijderen) tijdens een split-of samenvoeg bewerking

Nee. Voor **Split** -bewerkingen moet de doel database bestaan met het juiste schema en worden geregistreerd bij de Shard-kaart Manager.  Voor **samenvoeg** bewerkingen moet u de Shard verwijderen uit het Shard-toewijzings beheer en vervolgens de data base verwijderen.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]
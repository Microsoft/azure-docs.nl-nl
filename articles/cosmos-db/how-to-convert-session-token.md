---
title: Sessie-token indelingen converteren in .NET SDK-Azure Cosmos DB
description: Meer informatie over het converteren van sessie token indelingen om compatibiliteit tussen verschillende .NET SDK-versies te garanderen
author: vinhms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/30/2020
ms.author: vitrinh
ms.custom: devx-track-csharp
ms.openlocfilehash: 7ed06eeb7330989b4a251dc77e516eb8ac578bff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342042"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>Sessie-token indelingen converteren in .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt uitgelegd hoe u de verschillende indelingen van sessie tokens kunt converteren om compatibiliteit tussen SDK-versies te garanderen.

> [!NOTE]
> Standaard houdt de SDK het sessie token automatisch bij en wordt het meest recente sessie token gebruikt.  Ga voor meer informatie naar [gebruik sessie tokens](how-to-manage-consistency.md#utilize-session-tokens). De instructies in dit artikel zijn alleen van toepassing op de volgende voor waarden:
> * Uw Azure Cosmos DB-account maakt gebruik van sessie consistentie.
> * U beheert de sessie tokens hand matig.
> * U gebruikt op hetzelfde moment meerdere versies van de SDK.

## <a name="session-token-formats"></a>Sessie token indelingen

Er zijn twee notaties voor sessie tokens: **eenvoudig** en **Vector**.  Deze twee indelingen zijn niet uitwisselbaar. de indeling moet daarom worden geconverteerd wanneer aan de client toepassing met verschillende versies wordt door gegeven.
- De indeling van het **eenvoudige** sessie token wordt gebruikt door de .NET SDK V1 (Microsoft.Azure.DocumentDB-versie 1. x)
- De indeling van het **Vector** sessie token wordt gebruikt door de .NET SDK V2 (Microsoft.Azure.DocumentDB-versie 2. x)

### <a name="simple-session-token"></a>Eenvoudig sessie token

Een eenvoudig sessie token heeft de volgende indeling: `{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>Vector sessie token

Een vector sessie token heeft de volgende indeling: `{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>Converteren naar een eenvoudig sessie token

Als u een sessie token wilt door geven aan de client met behulp van .NET SDK v1, gebruikt u een **eenvoudige** sessie-token indeling.  Gebruik bijvoorbeeld de volgende voorbeeld code om deze te converteren.

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>Converteren naar vector sessie token

Als u een sessie token wilt door geven aan de client met behulp van .NET SDK v2, gebruikt u de indeling **Vector** -sessie token.  Gebruik bijvoorbeeld de volgende voorbeeld code om deze te converteren.

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen:

* [Sessie tokens gebruiken voor het beheren van consistentie in Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Kies het juiste consistentie niveau in Azure Cosmos DB](./consistency-levels.md)
* [Consistentie, Beschik baarheid en prestatie afweging in Azure Cosmos DB](./consistency-levels.md)
* [Beschik baarheid en prestaties voor diverse consistentie niveaus](./consistency-levels.md)
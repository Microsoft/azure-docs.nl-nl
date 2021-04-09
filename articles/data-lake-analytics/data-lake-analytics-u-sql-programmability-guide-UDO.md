---
title: Programmeer handleiding voor U-SQL UDO voor Azure Data Lake
description: Meer informatie over de programmeer baarheid van U-SQL UDO-Azure Data Lake Analytics om u in staat te stellen een goed USQL-script te maken.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512576"
---
# <a name="u-sql-user-defined-objects-overview"></a>Overzicht van door de gebruiker gedefinieerde U-SQL-objecten


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL: door de gebruiker gedefinieerde objecten: UDO
U kunt met u-SQL aangepaste programmeer baarheids objecten definiëren, die door de gebruiker gedefinieerde objecten of UDO worden genoemd.

Hier volgt een lijst met UDO in U-SQL:

* Door de gebruiker gedefinieerde extra heren
    * Rij per rij extra heren
    * Wordt gebruikt voor het implementeren van gegevens extractie vanuit aangepaste gestructureerde bestanden

* Door de gebruiker gedefinieerde outputters
    * Uitvoer rij per rij
    * Wordt gebruikt om aangepaste gegevens typen of aangepaste bestands indelingen uit te voeren

* Door de gebruiker gedefinieerde processors
    * Eén rij maken en een rij genereren
    * Hiermee wordt het aantal kolommen verminderd of worden nieuwe kolommen gegenereerd met waarden die zijn afgeleid van een bestaande kolomset

* Door de gebruiker gedefinieerde appliers
    * Eén rij maken en 0 tot n rijen opleveren
    * Wordt gebruikt met OUTER/CROSS APPLY

* Door de gebruiker gedefinieerde combi Naties
    * Combineert rijen sets--door de gebruiker gedefinieerde samen voegingen

* Door de gebruiker gedefinieerde verlaagers
    * N rijen nemen en één rij genereren
    * Hiermee wordt het aantal rijen verminderd

UDO wordt doorgaans expliciet genoemd in U-SQL-script als onderdeel van de volgende U-SQL-instructies:

* EXTRACT
* UITVOER
* HOST
* VERPLAATSEN
* BESPAREN

> [!NOTE]  
> UDO zijn beperkt tot 0,5 GB geheugen.  Deze beperking van het geheugen geldt niet voor lokale uitvoeringen.

## <a name="next-steps"></a>Volgende stappen
* [Programmeer handleiding U-SQL-overzicht](data-lake-analytics-u-sql-programmability-guide.md)
* [Programmeer handleiding voor U-SQL-UDT en UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
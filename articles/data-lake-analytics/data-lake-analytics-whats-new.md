---
title: Recente wijzigingen in Data Lake Analytics
description: Dit artikel bevat een lijst met recente wijzigingen die zijn aangebracht in Data Lake Analytics. De lijst wordt steeds bijgewerkt.
author: xujiang1
ms.service: data-lake-analytics
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: cc3f9623f3b09c14e0e478252768890e4de3acad
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025455"
---
# <a name="whats-new-in-data-lake-analytics"></a>Wat is er nieuw in Data Lake Analytics?

Azure Data Lake Analytics wordt van tijd tot tijd voor bepaalde onderdelen bijgewerkt. Als u op de hoogte wilt blijven van de nieuwste ontwikkelingen, biedt dit artikel u informatie over:

- Meldingen over béta-versies van belangrijke onderdelen
- Belangrijke informatie over onderdeelversies, zoals de lijst met beschikbare versies van het onderdeel, de huidige standaardversie, enzovoort.


## <a name="notification-of-key-component-beta-preview"></a>Melding over bèta-versies van belangrijke onderdelen

Er is geen bèta-versie van belangrijke onderdelen beschikbaar voor preview. 

## <a name="u-sql-runtime"></a>U-SQL-runtime

De Azure Data Lake U-SQL-runtime, met inbegrip van compiler, optimizer en taakbeheer, is datgene waarmee uw U-SQL-code wordt verwerkt.

Wanneer u de Azure Data Lake Analytics-taak vanuit een willekeurig hulpprogramma verzendt, gebruikt uw taak de momenteel beschikbare standaard-runtime in de productieomgeving. 

De runtime-versie wordt van tijd tot tijd bijgewerkt. En de vorige runtime blijft enige tijd beschikbaar. Wanneer een nieuwe bèta-versie gereed is voor preview, is deze hier ook beschikbaar.

> [!CAUTION]
> - Als u een runtime kiest die verschilt van de standaardinstelling, kunt u uw U-SQL-taken onderbreken. Het wordt ten zeerste aanbevolen om deze niet-standaard versies niet te gebruiken voor productie, maar alleen voor het testen.
> - De niet-standaard runtime-versie heeft een vaste levenscyclus. Deze verloopt automatisch.

De volgende versie is de huidige standaard runtime-versie.

- **release_20200707_scope_2b8d563_usql**

Raadpleeg [Problemen met U-SQL-runtime oplossen](runtime-troubleshoot.md) voor meer informatie over het oplossen van problemen met SQL-runtime-fouten.

## <a name="net-framework"></a>.NET Framework

Azure Data Lake Analytics maakt nu gebruik van **.NET Framework v4.7.2**. 

Als uw Azure Data Lake Analytics U-SQL-scriptcode gebruikmaakt van aangepaste assembly's, en deze aangepaste assembly's .NET-bibliotheken gebruiken, valideert u uw code om te controleren of er onderbrekingen zijn.

Meer informatie over het oplossen van problemen met een .NET-upgrade met [Problemen met een .NET-upgrade oplossen](runtime-troubleshoot.md).

## <a name="release-note"></a>Releaseopmerking

Raadpleeg de [Releaseopmerking bij Azure Data Lake Analytics](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes) voor recente updatedetails.


## <a name="next-steps"></a>Volgende stappen

* Aan de slag met Data Lake Analytics met behulp van [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli.md)


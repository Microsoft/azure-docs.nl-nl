---
title: Bekende problemen en beperkingen-Azure Database for PostgreSQL-één server en flexibele server (preview-versie)
description: Een lijst met bekende problemen waarvan klanten rekening moeten houden.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106406"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL-bekende problemen en beperkingen

Op deze pagina vindt u een lijst met bekende problemen in Azure Database for PostgreSQL die van invloed kunnen zijn op uw toepassing. Ook vindt u hier een lijst met mogelijke oplossingen en aanbevelingen voor het oplossen van het probleem.

## <a name="intelligent-performance---query-store"></a>Intelligente prestaties-query Store

Van toepassing op Azure Database for PostgreSQL-één server.

| Van toepassing | Oorzaak | Herstel|
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | Het inschakelen van de server parameter `pg_qs.replace_parameter_placeholders` kan leiden tot het afsluiten van een server in sommige zeldzame scenario's. | Via de Azure-Portal, de sectie server parameters, zet u de parameter `pg_qs.replace_parameter_placeholders` waarde op en slaat u deze op `OFF` .   | 

## <a name="server-parameters"></a>Serverparameters

Van toepassing op Azure Database for PostgreSQL-één server en flexibele server

| Van toepassing | Oorzaak | Herstel| 
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | Het wijzigen van de server parameter `max_locks_per_transaction` naar een hogere waarde dan wat er wordt [Aanbevolen](https://www.postgresql.org/docs/11/kernel-resources.html) , kan ertoe leiden dat de server niet meer kan worden gestart nadat de computer opnieuw is opgestart. | U kunt de standaard waarde (32 of 64) behouden of overschakelen naar een redelijke waarde per PostgreSQL- [documentatie](https://www.postgresql.org/docs/11/kernel-resources.html). <br> <br> Aan de kant van de service wordt dit aan gewerkt om de hoge waarde te beperken op basis van de SKU.  | 

## <a name="next-steps"></a>Volgende stappen
- Zie [Aanbevolen procedures](./concepts-query-store-best-practices.md) voor query Store

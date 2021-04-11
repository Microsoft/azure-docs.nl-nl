---
title: Ondersteunde versies – grootschalige (Citus)-Azure Database for PostgreSQL
description: PostgreSQL-versies beschikbaar in Azure Database for PostgreSQL-grootschalige (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d8a584b6ba752e8f9220defa575f519828ba07e6
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023944"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Ondersteunde database versies in Azure Database for PostgreSQL – grootschalige (Citus)

## <a name="postgresql-versions"></a>PostgreSQL-versies

> [!IMPORTANT]
> Aanpas bare PostgreSQL-versies in grootschalige (Citus) zijn momenteel beschikbaar als preview-versie.  Deze preview wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
>
> U kunt een volledige lijst met andere nieuwe functies bekijken in [Preview-functies voor grootschalige (Citus)](hyperscale-preview-features.md).

De versie van PostgreSQL die wordt uitgevoerd in een grootschalige-Server groep (Citus), is aanpasbaar tijdens het maken. Een andere optie dan versie 11 is momenteel een preview-functie.

Grootschalige (Citus) ondersteunt momenteel de volgende primaire versies:

### <a name="postgresql-version-13-preview"></a>PostgreSQL versie 13 (preview)

De huidige secundaire versie is 13,2. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/13/static/release-13-2.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

### <a name="postgresql-version-12-preview"></a>PostgreSQL-versie 12 (preview)

De huidige secundaire versie is 12,6. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/12/static/release-12-6.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

### <a name="postgresql-version-11"></a>PostgreSQL-versie 11

De huidige secundaire versie is 11,11. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/11/static/release-11-11.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

### <a name="postgresql-version-10-and-older"></a>PostgreSQL-versie 10 en ouder

PostgreSQL versie 10 en ouder voor Azure Database for PostgreSQL-grootschalige (Citus) worden niet ondersteund.

## <a name="citus-and-other-extension-versions"></a>Citus en andere extensie versies

Afhankelijk van welke versie van PostgreSQL wordt uitgevoerd in een server groep, worden ook verschillende [versies van post gres-extensies](concepts-hyperscale-extensions.md) geïnstalleerd.  Met name post gres 13 wordt geleverd met Citus 10 en eerdere versies van post gres worden geleverd met Citus 9,5.

## <a name="next-steps"></a>Volgende stappen

* Bekijk welke [uitbrei dingen](concepts-hyperscale-extensions.md) zijn geïnstalleerd in welke versies.
* Meer informatie over [het maken van een grootschalige (Citus)-Server groep](quickstart-create-hyperscale-portal.md).

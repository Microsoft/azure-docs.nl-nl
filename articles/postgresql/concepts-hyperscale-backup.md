---
title: Back-up en herstel - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Gegevens beschermen tegen onbedoelde beschadiging of verwijdering
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 8dfc82ce79f33553be5220b52a1e415d99c26518
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483908"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Back-up en herstel in Azure Database for PostgreSQL - Hyperscale (Citus)

Azure Database for PostgreSQL: Hyperscale (Citus) maakt automatisch back-ups van elk knooppunt en slaat deze op in lokaal redundante opslag. Back-ups kunnen worden gebruikt om uw Hyperscale (Citus) te herstellen naar een opgegeven tijd. Back-ups maken en herstellen zijn essentiële onderdelen van een strategie voor bedrijfscontinuïteit omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering.

## <a name="backups"></a>Back-ups

Ten minste één keer per dag maakt Azure Database for PostgreSQL momentopnameback-ups van gegevensbestanden en het transactielogboek van de database. Met de back-ups kunt u een server herstellen naar een bepaald tijdstip binnen de bewaarperiode. (De bewaarperiode is momenteel 35 dagen voor alle clusters.) Alle back-ups worden versleuteld met AES 256-bits versleuteling.

In Azure-regio's die beschikbaarheidszones ondersteunen, worden momentopnamen van back-ups opgeslagen in drie beschikbaarheidszones. Zolang ten minste één beschikbaarheidszone online is, kan Hyperscale (Citus) cluster worden terugplaatst.

Back-upbestanden kunnen niet worden geëxporteerd. Ze kunnen alleen worden gebruikt voor herstelbewerkingen in Azure Database for PostgreSQL.

### <a name="backup-storage-cost"></a>Kosten voor back-upopslag

Zie de pagina met prijzen voor Azure Database for PostgreSQL - Hyperscale (Citus) voor de huidige [prijzen voor back-upopslag.](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)

## <a name="restore"></a>Herstellen

Als Azure Database for PostgreSQL cluster herstelt Hyperscale (Citus) cluster, wordt er een nieuw cluster gemaakt op de back-ups van de oorspronkelijke knooppunten. 

> [!IMPORTANT]
>U kunt de Hyperscale (Citus) alleen herstellen binnen hetzelfde abonnement en dezelfde resourcegroep, en met een andere clusternaam.


> [!IMPORTANT]
> Verwijderde Hyperscale (Citus) kunnen niet worden hersteld. Als u het cluster verwijdert, worden alle knooppunten verwijderd die deel uitmaken van het cluster en kunnen ze niet worden hersteld. Beheerders kunnen beheervergrendelingen gebruiken om clusterbronnen na de implementatie te beschermen tegen onbedoelde verwijdering [of onverwachte wijzigingen.](../azure-resource-manager/management/lock-resources.md)

### <a name="point-in-time-restore-pitr"></a>Herstel naar een bepaald tijdstip (PITR)

U kunt een cluster herstellen naar elk tijdstip in de afgelopen 35 dagen.
Herstel naar een bepaald tijdstip is nuttig in meerdere scenario's. Wanneer een gebruiker bijvoorbeeld per ongeluk gegevens wist, een belangrijke tabel of database verwijdert of als een toepassing per ongeluk goede gegevens overschrijft met ongeldige gegevens.

Tijdens het herstelproces wordt een nieuw cluster gemaakt in dezelfde Azure-regio, hetzelfde abonnement en dezelfde resourcegroep als de oorspronkelijke. Het cluster heeft de configuratie van het origineel: hetzelfde aantal knooppunten, aantal vCores, opslaggrootte, gebruikersrollen, PostgreSQL-versie en versie van de Citus-extensie.

Firewallinstellingen en PostgreSQL-serverparameters blijven niet behouden in de oorspronkelijke servergroep. Ze worden opnieuw ingesteld op de standaardwaarden. De firewall voorkomt alle verbindingen. U moet deze instellingen na het herstellen handmatig aanpassen. Zie in het algemeen onze lijst met voorgestelde [taken na herstel.](howto-hyperscale-restore-portal.md#post-restore-tasks)

## <a name="next-steps"></a>Volgende stappen

* Zie de stappen voor [het herstellen van een servergroep](howto-hyperscale-restore-portal.md) in de Azure Portal.
* Meer informatie over [Azure-beschikbaarheidszones.](../availability-zones/az-overview.md)

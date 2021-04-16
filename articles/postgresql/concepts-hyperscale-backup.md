---
title: Back-up en herstel - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Gegevens beschermen tegen onbedoelde beschadiging of verwijdering
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 7681e9c28bbbbcec06bcc1cf2bf469f1b4189d79
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520170"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Back-up en herstel in Azure Database for PostgreSQL - Hyperscale (Citus)

Azure Database for PostgreSQL: Hyperscale (Citus) maakt automatisch back-ups van elk knooppunt en slaat deze op in lokaal redundante opslag. Back-ups kunnen worden gebruikt om uw Hyperscale (Citus) servergroep te herstellen naar een opgegeven tijd.
Back-ups maken en herstellen zijn essentiële onderdelen van een strategie voor bedrijfscontinuïteit omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering.

## <a name="backups"></a>Back-ups

Ten minste één keer per dag maakt Azure Database for PostgreSQL momentopnameback-ups van gegevensbestanden en het transactielogboek van de database. Met de back-ups kunt u een server herstellen naar een bepaald tijdstip binnen de bewaarperiode. (De bewaarperiode is momenteel 35 dagen voor alle servergroepen.) Alle back-ups worden versleuteld met AES 256-bits versleuteling.

In Azure-regio's die beschikbaarheidszones ondersteunen, worden momentopnamen van back-ups opgeslagen in drie beschikbaarheidszones. Zolang ten minste één beschikbaarheidszone online is, kan Hyperscale (Citus) servergroep opnieuw worden ingesteld.

Back-upbestanden kunnen niet worden geëxporteerd. Ze kunnen alleen worden gebruikt voor herstelbewerkingen in Azure Database for PostgreSQL.

### <a name="backup-storage-cost"></a>Kosten voor back-upopslag

Zie de pagina met prijzen voor Azure Database for PostgreSQL - Hyperscale (Citus) voor de huidige [prijzen voor back-upopslag.](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)

## <a name="restore"></a>Herstellen

U kunt een Hyperscale (Citus) servergroep herstellen naar elk tijdstip in de afgelopen 35 dagen.  Herstel naar een bepaald tijdstip is handig in meerdere scenario's. Wanneer een gebruiker bijvoorbeeld per ongeluk gegevens wist, een belangrijke tabel of database verwijdert of als een toepassing per ongeluk goede gegevens overschrijft met ongeldige gegevens.

> [!IMPORTANT]
> Verwijderde Hyperscale (Citus) kunnen niet worden hersteld. Als u de servergroep verwijdert, worden alle knooppunten verwijderd die deel uitmaken van de servergroep en kunnen ze niet worden hersteld. Beheerders kunnen beheervergrendelingen gebruiken om resources van de servergroep na de implementatie te beschermen tegen onbedoelde verwijdering [of onverwachte wijzigingen.](../azure-resource-manager/management/lock-resources.md)

Tijdens het herstelproces wordt een nieuwe servergroep gemaakt in dezelfde Azure-regio, hetzelfde abonnement en dezelfde resourcegroep als de oorspronkelijke. De servergroep heeft de configuratie van het origineel: hetzelfde aantal knooppunten, aantal vCores, opslaggrootte, gebruikersrollen, PostgreSQL-versie en versie van de Citus-extensie.

Firewallinstellingen en PostgreSQL-serverparameters blijven niet behouden in de oorspronkelijke servergroep. Ze worden opnieuw ingesteld op de standaardwaarden. De firewall voorkomt alle verbindingen. U moet deze instellingen na het herstellen handmatig aanpassen. Zie in het algemeen onze lijst met voorgestelde [taken na herstel.](howto-hyperscale-restore-portal.md#post-restore-tasks)

## <a name="next-steps"></a>Volgende stappen

* Zie de stappen voor [het herstellen van een servergroep](howto-hyperscale-restore-portal.md) in de Azure Portal.
* Meer informatie over [Azure-beschikbaarheidszones.](../availability-zones/az-overview.md)

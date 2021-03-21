---
title: Bewaking en metrische gegevens-Azure Database for PostgreSQL-flexibele server
description: In dit artikel worden de functies bewaking en metrische gegevens van Azure Database for PostgreSQL-flexibele server beschreven.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 5e7063cd1ae560fa077bd0b1b1279e4515e70464
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579013"
---
# <a name="monitor-metrics-on-azure-database-for-postgresql---flexible-server"></a>Metrische gegevens controleren op Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Het bewaken van gegevens over uw servers helpt u bij het oplossen en optimaliseren van uw werk belasting. Azure Database for PostgreSQL biedt verschillende bewakings opties om inzicht te krijgen in het gedrag van uw server.

## <a name="metrics"></a>Metrische gegevens
Azure Database for PostgreSQL biedt diverse metrische gegevens die inzicht geven in het gedrag van de bronnen die de PostgreSQL-Server ondersteunen. Elke metriek wordt verzonden met een frequentie van één minuut en heeft een [geschiedenis van Maxi maal 93 dagen](../../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics). U kunt waarschuwingen configureren voor de metrische gegevens. Andere opties zijn het instellen van geautomatiseerde acties, het uitvoeren van geavanceerde analyses en het archiveren van de geschiedenis. Zie het overzicht van Azure- [metrische](../../azure-monitor/essentials/data-platform-metrics.md)gegevens voor meer informatie.

### <a name="list-of-metrics"></a>Lijst met metrische gegevens
De volgende metrische gegevens zijn beschikbaar voor PostgreSQL flexibele server:


|Metrisch|Weergave naam voor metrische gegevens|Eenheid|Beschrijving|
|---|---|---|---|
| active_connections | Actieve verbindingen | Count | Het aantal verbindingen met uw server. | 
| backup_storage_used | Gebruikte back-upopslag | Bytes | Hoeveelheid gebruikte back-upopslag. Deze waarde vertegenwoordigt de som van de opslag die wordt gebruikt door alle back-ups van de volledige data base, differentiële back-ups en logboek back-ups die worden bewaard op basis van de Bewaar periode voor back-ups die is ingesteld voor de server. De frequentie van de back-ups is service beheerd. Voor geo-redundante opslag is het gebruik van back-upopslag twee keer zo dat van de lokaal redundante opslag. |
| connections_failed | Mislukte verbindingen | Count | Mislukte verbindingen. |
| connections_succeeded | Geslaagde verbindingen | Count | Geslaagde verbindingen. |
| cpu_credits_consumed | Verbruikt CPU-tegoed | Count | Het aantal tegoeden dat door de flexibele server wordt gebruikt. Van toepassing op de Burstable-laag. |
| cpu_credits_remaining | Resterend CPU-tegoed | Count | Aantal beschik bare tegoeden voor burst. Van toepassing op de Burstable-laag. |
| cpu_percent | CPU-percentage | Percentage | Percentage CPU-gebruik. | 
| disk_queue_depth | Schijf wachtrij diepte | Count | Aantal openstaande I/O-bewerkingen op de gegevens schijf. |
| IOPS | IOPS | Count | Aantal I/O-bewerkingen op de schijf per seconde. |
| maximum_used_transactionIDs | Maximum aantal gebruikte trans actie-Id's | Count | De maximale trans actie-ID die wordt gebruikt. |
| memory_percent | Geheugen percentage | Percentage | Het percentage geheugen dat in gebruik is. |
| network_bytes_egress | Netwerk uit | Bytes | Hoeveelheid uitgaand netwerk verkeer. |
| network_bytes_ingress | Netwerk in | Bytes | Hoeveelheid binnenkomend netwerk verkeer. |
| read_iops | IOPS lezen | Count | Aantal I/O-lees bewerkingen per seconde van gegevens schijf. |
| read_throughput | Lees doorvoer | Bytes | Bytes gelezen per seconde van de schijf. |
| storage_free | Vrije opslag ruimte | Bytes | De hoeveelheid beschik bare opslag ruimte. |
| storage_percent | Opslag percentage | Percentage | Percentage gebruikte opslag ruimte. De opslag die door de service wordt gebruikt, kan de database bestanden, transactie logboeken en de server logboeken bevatten.|
| storage_used | Gebruikte opslag | Bytes | Percentage gebruikte opslag ruimte. De opslag die door de service wordt gebruikt, kan de database bestanden, transactie logboeken en de server logboeken bevatten. |
| txlogs_storage_used | Gebruikte transactie logboek opslag | Bytes | De hoeveelheid opslag ruimte die wordt gebruikt door de transactie Logboeken. | 
| write_throughput | Schrijf doorvoer | Bytes | Geschreven bytes per seconde naar schijf. |
| write_iops | IOPS schrijven | Count | Aantal I/O-schrijf bewerkingen per seconde van de gegevens schijf. |

## <a name="server-logs"></a>Serverlogboeken
Met Azure Database for PostgreSQL kunt u standaard logboeken van post gres configureren en gebruiken. Voor meer informatie over Logboeken gaat u naar het [document logboek registratie concepten](concepts-logging.md).

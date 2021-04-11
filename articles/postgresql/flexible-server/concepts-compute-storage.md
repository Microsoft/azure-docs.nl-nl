---
title: Berekenings-en opslag opties-Azure Database for PostgreSQL-flexibele server
description: In dit artikel worden de berekenings-en opslag opties in Azure Database for PostgreSQL flexibele server beschreven.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 93a439c814b6bac630c5231f46d7a022a131687c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608167"
---
# <a name="compute-and-storage-options-in-azure-database-for-postgresql---flexible-server"></a>Berekenings-en opslag opties in Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

U kunt in een van de volgende drie verschillende prijs categorieën een Azure Database for PostgreSQL-server maken: Burstable, Algemeen en geoptimaliseerd voor geheugen. De prijs categorieën worden onderscheiden van de hoeveelheid Compute in vCores die kan worden ingericht, het geheugen per vCore en de opslag technologie die wordt gebruikt om de gegevens op te slaan. Alle resources worden ingericht op het niveau van de PostgreSQL-server. Een server kan een of meer data bases bevatten.

| Resource/laag | **Bebreekbaar** | **Algemeen doel** | **Geoptimaliseerd voor geheugen** |
|:---|:----------|:--------------------|:---------------------|
| vCores | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Geheugen per vCore | Variabele | 4 GB | 6,75 tot 8 GB |
| Opslag grootte | 32 GB tot 16 TB | 32 GB tot 16 TB | 32 GB tot 16 TB |
| Bewaar periode voor database back-ups | 7 tot 35 dagen | 7 tot 35 dagen | 7 tot 35 dagen |

Als u een prijs categorie wilt kiezen, gebruikt u de volgende tabel als uitgangs punt.

| Prijscategorie | Beoogde workloads |
|:-------------|:-----------------|
| Bebreekbaar | Het beste voor werk belastingen waarvoor de volledige CPU niet continu nodig is. |
| Algemeen gebruik | De meeste zakelijke workloads die evenwichtige reken kracht en geheugen vereisen met schaal bare I/O-door voer. Voorbeelden zijn onder meer servers voor het hosten van web- en mobiele apps en andere zakelijke toepassingen.|
| Geoptimaliseerd geheugen | Data bases met hoogwaardige prestaties waarvoor de prestaties in het geheugen zijn vereist voor een snellere transactie verwerking en hogere gelijktijdigheid. Voorbeelden zijn onder meer servers voor het verwerken van realtime gegevens en transactionele of analytische toepassingen met hoge prestaties.|

Nadat u een server hebt gemaakt, kunnen de compute-laag, het aantal vCores en de opslag grootte binnen enkele seconden worden gewijzigd. Daarnaast kunt u de Bewaar periode voor back-ups onafhankelijk of omlaag aanpassen. Zie de sectie [resources schalen](#scale-resources) voor meer informatie.

## <a name="compute-tiers-vcores-and-server-types"></a>Reken lagen, vCores en server typen

Reken resources kunnen worden geselecteerd op basis van de laag, de vCores en de grootte van het geheugen. vCores vertegenwoordigen de logische CPU van de onderliggende hardware.

De gedetailleerde specificaties van de beschik bare server typen zijn als volgt:

| SKU-naam             | vCores | Geheugen grootte | Maxi maal ondersteunde IOPS | Maxi maal ondersteunde I/O-band breedte |
|----------------------|--------|-------------|--------------------|-----------------------------|
| **Bebreekbaar**        |        |             |                    |                             |
| B1ms                 | 1      | 2 GiB       | 640                | 15 MiB per seconde                  |
| B2s                  | 2      | 4 GiB       | 1280               | 15 MiB per seconde                  |
| **Algemeen doel**  |        |             |                    |                             |
| D2s_v3               | 2      | 8 GiB       | 3200               | 48-MiB per seconde                  |
| D4s_v3               | 4      | 16 GiB      | 6400               | 96-MiB per seconde                  |
| D8s_v3               | 8      | 32 GiB      | 12800              | 192-MiB per seconde                 |
| D16s_v3              | 16     | 64 GiB      | 18000              | 384-MiB per seconde                 |
| D32s_v3              | 32     | 128 GiB     | 18000              | 750-MiB per seconde                 |
| D48s_v3              | 48     | 192 GiB     | 18000              | 750-MiB per seconde                 |
| D64s_v3              | 64     | 256 GiB     | 18000              | 750-MiB per seconde                 |
| **Geoptimaliseerd voor geheugen** |        |             |                    |                             |
| E2s_v3               | 2      | 16 GiB      | 3200               | 48-MiB per seconde                  |
| E4s_v3               | 4      | 32 GiB      | 6400               | 96-MiB per seconde                  |
| E8s_v3               | 8      | 64 GiB      | 12800              | 192-MiB per seconde                 |
| E16s_v3              | 16     | 128 GiB     | 18000              | 384-MiB per seconde                 |
| E32s_v3              | 32     | 256 GiB     | 18000              | 750-MiB per seconde                 |
| E48s_v3              | 48     | 384 GiB     | 18000              | 750-MiB per seconde                 |
| E64s_v3              | 64     | 432 GiB     | 18000              | 750-MiB per seconde                 |

## <a name="storage"></a>Storage

De opslag ruimte die u inricht, is de hoeveelheid opslag capaciteit die beschikbaar is voor uw Azure Database for PostgreSQL-server. De opslag wordt gebruikt voor de database bestanden, tijdelijke bestanden, transactie logboeken en de PostgreSQL-server Logboeken. De totale hoeveelheid opslag ruimte die u hebt ingericht, definieert ook de I/O-capaciteit die beschikbaar is voor uw server.

Opslag is beschikbaar in de volgende vaste groottes:

| Schijfgrootte | IOPS |
|:---|:---|
| 32 GiB | Ingericht 120, Maxi maal 3.500 |
| 64 GiB | Ingericht 240, Maxi maal 3.500 |
| 128 GiB | Ingericht 500, Maxi maal 3.500 |
| 256 GiB | Ingericht 1100, Maxi maal 3.500 |
| 512 GiB | Ingericht 2300, Maxi maal 3.500 |
| 1 TiB | 5\.000 |
| 2 TiB | 7\.500 |
| 4 TiB | 7\.500 |
| 8 TiB | 16.000 |
| 16 TiB | 18.000 |

Houd er rekening mee dat IOPS ook worden beperkt door uw VM-type. Hoewel u een wille keurige opslag grootte kunt selecteren, onafhankelijk van het server type, kunt u mogelijk niet alle IOPS gebruiken die de opslag biedt, met name wanneer u een server met een klein aantal vCores kiest.

U kunt extra opslag capaciteit toevoegen tijdens en na het maken van de-server.

>[!NOTE]
> Opslag kan alleen omhoog en niet omlaag worden geschaald.

U kunt uw I/O-gebruik bewaken in de Azure Portal of met behulp van Azure CLI-opdrachten. De relevante metrische gegevens die moeten worden bewaakt [, zijn opslag limiet, opslag percentage, gebruikte opslag en i/o-percentage](concepts-monitoring.md).

### <a name="maximum-iops-for-your-configuration"></a>Maximale IOPS voor uw configuratie

|SKU-naam            |Opslag grootte in GiB                       |32 |64 |128 |256 |512  |1\.024|2048|4.096|8\.192 |16.384|
|--------------------|------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |Maximum aantal IOPS                              |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|**Bebreekbaar**       |                                          |   |   |    |    |     |     |     |     |      |      |
|B1ms                |640 IOPS                                  |120|240|500 |640 *|640 * |640 * |640 * |640 * |640 *  |640 *  |
|B2s                 |1280 IOPS                                 |120|240|500 |1100|1280 *|1280 *|1280 *|1280 *|1280 * |1280 * |
|**Algemeen doel** |                                          |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |3200 IOPS                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|D4s_v3              |6.400 IOPS                                |120|240|500 |1100|2300 |5000 |6400 *|6400 *|6400 * |6400 * |
|D8s_v3              |12.800 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |12800 *|12800 *|
|D16s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D32s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D48s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D64s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|**Geoptimaliseerd voor geheugen**|                                          |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |3200 IOPS                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|E4s_v3              |6.400 IOPS                                |120|240|500 |1100|2300 |5000 |6400 *|6400 *|6400 * |6400 * |
|E8s_v3              |12.800 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |12800 *|12800 *|
|E16s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E32s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E48s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E64s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |

Wanneer het is gemarkeerd met een \* , worden IOPS beperkt door het VM-type dat u hebt geselecteerd. Anders wordt IOPS beperkt door de geselecteerde opslag grootte.

>[!NOTE]
> Als gevolg van bursting op schijf niveau worden er meer IOPS in de metrische gegevens weer gegeven. Raadpleeg de [documentatie](../../virtual-machines/disk-bursting.md#disk-level-bursting) voor meer informatie. 

### <a name="maximum-io-bandwidth-mibsec-for-your-configuration"></a>Maximale I/O-band breedte (MiB/sec) voor uw configuratie

|SKU-naam            |Opslag grootte, GiB                             |32 |64 |128 |256 |512  |1\.024|2048|4.096|8\.192 |16.384|
|--------------------|----------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |**Opslag bandbreedte, MiB per seconde**                |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Bebreekbaar**       |                                              |   |   |    |    |     |     |     |     |      |      |
|B1ms                |10 MiB per seconde                                    |6|6|6 |6 |6  |6  |6  |6  |6   |6   |
|B2s                 |15 MiB per seconde                                    |15|15|15 |15 |15  |15  |15  |15  |15   |15   |
|**Algemeen doel** |                                              |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |48-MiB per seconde                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|D4s_v3              |96-MiB per seconde                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|D8s_v3              |192-MiB per seconde                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|D16s_v3             |384-MiB per seconde                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|D32s_v3             |750-MiB per seconde                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D48s_v3             |750-MiB per seconde                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D64s_v3             |750-MiB per seconde                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Geoptimaliseerd voor geheugen**|                                              |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |48-MiB per seconde                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|E4s_v3              |96-MiB per seconde                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|E8s_v3              |192-MiB per seconde                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|E16s_v3             |384-MiB per seconde                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|E32s_v3             |750-MiB per seconde                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E48s_v3             |750-MiB per seconde                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E64s_v3             |750-MiB per seconde                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |

Als \* u een I/O-band breedte hebt gemarkeerd, wordt deze beperkt door het VM-type dat u hebt geselecteerd. Anders wordt de I/O-band breedte beperkt door de geselecteerde opslag grootte.

### <a name="reaching-the-storage-limit"></a>De opslaglimiet wordt bereikt

Wanneer u de opslag limiet bereikt, wordt het starten van fouten door de server gestart en wordt voor komen dat er verdere wijzigingen worden aangebracht. Dit kan ook problemen veroorzaken met andere operationele activiteiten, zoals back-ups en WAL archivering.

Om deze situatie te voor komen, wordt de server automatisch overgeschakeld naar de **modus alleen-lezen** wanneer het opslag gebruik 95% bereikt of als de beschik bare capaciteit minder dan 5 GiB is.

We raden u aan om de schijf ruimte die in gebruik is, actief te bewaken en de schijf grootte te verhogen van de opslag situatie. U kunt een waarschuwing instellen om u te waarschuwen wanneer uw server opslag bijna geen schijf meer benadert, zodat u problemen met het uitvoeren van de schijf kunt voor komen. Zie de documentatie over het [instellen van een waarschuwing](howto-alert-on-metrics.md)voor meer informatie.


### <a name="storage-auto-grow"></a>Opslag automatisch verg Roten

Opslag automatisch uitbreiden is nog niet beschikbaar voor flexibele servers.

## <a name="backup"></a>Backup

De service maakt automatisch back-ups van uw server. U kunt een Bewaar periode van 7 tot 35 dagen selecteren. Meer informatie over back-ups vindt u in het [artikel concepten](concepts-backup-restore.md).

## <a name="scale-resources"></a>Resources schalen

Nadat u de server hebt gemaakt, kunt u onafhankelijk de vCores, de compute-laag, de hoeveelheid opslag ruimte en de Bewaar periode voor back-ups wijzigen. Het aantal vCores kan omhoog of omlaag worden geschaald. De Bewaar periode van de back-up kan worden uitgebreid of omlaag van 7 tot 35 dagen. De opslag grootte kan alleen worden verhoogd. U kunt de resources verg Roten of verkleinen via de portal of Azure CLI.

> [!NOTE]
> De opslag grootte kan alleen worden verhoogd. U kunt niet teruggaan naar een kleinere opslag grootte na de verhoging.

Wanneer u het aantal vCores of de compute-laag wijzigt, wordt de server opnieuw opgestart om het nieuwe server type van kracht te laten worden. Op het moment dat het systeem overschakelt naar de nieuwe server, kunnen er geen nieuwe verbindingen worden vastgelegd en worden alle niet-doorgevoerde transacties teruggedraaid. Dit tijdvenster is variabel, maar is in de meeste gevallen minder dan een minuut. Het schalen van de opslag werkt op dezelfde manier en er moet ook een korte herstart worden uitgevoerd.

Het wijzigen van de Bewaar periode voor back-ups is een online bewerking.

## <a name="pricing"></a>Prijzen

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/PostgreSQL/)voor services voor de meest actuele prijs informatie. Als u de kosten voor de gewenste configuratie wilt zien, geeft de [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) de maandelijkse kosten op het tabblad **prijs categorie** weer op basis van de opties die u selecteert. Als u geen Azure-abonnement hebt, kunt u de Azure-prijs calculator gebruiken om een geschatte prijs te krijgen. Selecteer op de website [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) de optie **items toevoegen**, vouw de categorie **data bases** uit en kies **Azure database for PostgreSQL** om de opties aan te passen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een postgresql-server in de portal](how-to-manage-server-portal.md).
- Meer informatie over [service limieten](concepts-limits.md).
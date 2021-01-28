---
title: Berekenings-en opslag opties-Azure Database for MySQL-flexibele server
description: In dit artikel worden de berekenings-en opslag opties in Azure Database for MySQL flexibele server beschreven.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 267b362c94b04b3be634f7e61c2b6d67604d7854
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954678"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Berekenings-en opslag opties in Azure Database for MySQL-flexibele server (preview-versie)

> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

U kunt een Azure Database for MySQL flexibele server maken in een van de drie verschillende reken lagen: Burstable, Algemeen en geoptimaliseerd voor geheugen. De reken lagen worden onderscheiden door de onderliggende VM-SKU gebruikte de B-serie, D-serie en E-Series. De gekozen reken laag en grootte bepalen het geheugen en de vCores die beschikbaar zijn op de server. Dezelfde opslag technologie wordt gebruikt in alle reken lagen. Alle resources worden ingericht op het niveau van de MySQL-server. Een server kan een of meer data bases bevatten.

| Resource/laag | **Bebreekbaar** | **Algemeen doel** | **Geoptimaliseerd voor geheugen** |
|:---|:----------|:--------------------|:---------------------|
| VM-reeks| B-serie | Ddsv4-serie | Edsv4-serie|
| vCores | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Geheugen per vCore | Variabele | 4 GiB | 8 GiB * |
| Opslag grootte | 5 GiB tot 16 TiB | 5 GiB tot 16 TiB | 5 GiB tot 16 TiB |
| Bewaar periode voor database back-ups | 1 tot 35 dagen | 1 tot 35 dagen | 1 tot 35 dagen |

\* Met uitzonde ring van E64ds_v4 SKU (geoptimaliseerd voor geheugen), met 504 GB geheugen

Als u een compute-laag wilt kiezen, gebruikt u de volgende tabel als uitgangs punt.

| Compute-laag | Beoogde workloads |
|:-------------|:-----------------|
| Bebreekbaar | Het beste voor werk belastingen waarvoor de volledige CPU niet continu nodig is. |
| Algemeen gebruik | De meeste zakelijke workloads die evenwichtige reken kracht en geheugen vereisen met schaal bare I/O-door voer. Voorbeelden zijn onder meer servers voor het hosten van web- en mobiele apps en andere zakelijke toepassingen.|
| Geoptimaliseerd geheugen | Data bases met hoogwaardige prestaties waarvoor de prestaties in het geheugen zijn vereist voor een snellere transactie verwerking en hogere gelijktijdigheid. Voorbeelden zijn onder meer servers voor het verwerken van realtime gegevens en transactionele of analytische toepassingen met hoge prestaties.|

Nadat u een server hebt gemaakt, zijn de compute-laag, de reken grootte en de opslag grootte gewijzigd. Voor het schalen van de berekening moet de computer opnieuw worden opgestart en het duurt tussen 60-120 seconden, terwijl voor het schalen van de opslag niet opnieuw moet worden opgestart. Daarnaast kunt u de Bewaar periode voor back-ups onafhankelijk of omlaag aanpassen. Zie de sectie [resources schalen](#scale-resources) voor meer informatie.

## <a name="compute-tiers-size-and-server-types"></a>Reken lagen, grootte en server typen

Reken resources kunnen worden geselecteerd op basis van de laag en grootte. Hiermee bepaalt u de vCores en de geheugen grootte. vCores vertegenwoordigen de logische CPU van de onderliggende hardware.

De gedetailleerde specificaties van de beschik bare server typen zijn als volgt:

| Reken grootte         | vCores | Geheugen grootte (GiB) | 
|----------------------|--------|-------------------|
| **Bebreekbaar**        |        |                   | 
| Standard_B1s         | 1      | 1                 |  
| Standard_B1ms        | 1      | 2                 | 
| Standard_B2s         | 2      | 4                 |  
| **Algemeen doel**  |        |                   | 
| Standard_D2ds_v4     | 2      | 8                 |  
| Standard_D4ds_v4     | 4      | 16                | 
| Standard_D8ds_v4     | 8      | 32                | 
| Standard_D16ds_v4    | 16     | 64                | 
| Standard_D32ds_v4    | 32     | 128               |  
| Standard_D48ds_v4    | 48     | 192               |  
| Standard_D64ds_v4    | 64     | 256               | 
| **Geoptimaliseerd voor geheugen** |        |                   |
| Standard_E2ds_v4     | 2      | 16                |
| Standard_E4ds_v4     | 4      | 32                |
| Standard_E8ds_v4     | 8      | 64                |
| Standard_E16ds_v4    | 16     | 128               |
| Standard_E32ds_v4    | 32     | 256               |
| Standard_E48ds_v4    | 48     | 384               |
| Standard_E64ds_v4    | 64     | 504               |

Voor meer informatie over de beschik bare reken reeks raadpleegt u de Azure VM-documentatie voor de [Burstable (B-serie)](../../virtual-machines/sizes-b-series-burstable.md), [Algemeen (Ddsv4-serie)](../../virtual-machines/ddv4-ddsv4-series.md)en [geoptimaliseerd voor geheugen (Edsv4-serie)](../../virtual-machines/edv4-edsv4-series.md).

>[!NOTE]
>Voor een [afbreek bare (B-serie)](../../virtual-machines/sizes-b-series-burstable.md) Compute-laag wanneer de server om een andere reden opnieuw wordt opgestart, zoals gestart, gepland of niet-gepland onderhoud, kan het geaccumuleerde tegoed verloren gaan. De reden hiervoor is dat wanneer de Azure Database for MySQL opnieuw wordt opgestart, deze op hetzelfde knoop punt blijft, wordt het geaccumuleerde tegoed bewaard. Daarentegen, wanneer de Azure Database for MySQL-server op een nieuw knoop punt wordt vernieuwd, ontvangt deze een eerste tegoed. Zie Veelgestelde vragen voor meer informatie Lees [Bebreek de FAQ (B-serie)](https://docs.microsoft.com/azure/virtual-machines/sizes-b-series-burstable#q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart).

## <a name="storage"></a>Storage

De opslag ruimte die u hebt ingericht, is de opslag capaciteit die beschikbaar is voor uw flexibele server. Opslag wordt gebruikt voor de database bestanden, tijdelijke bestanden, transactie logboeken en de MySQL-server Logboeken. In alle reken lagen is de minimale opslag die wordt ondersteund, 5 GiB en maximum 16 TiB. Opslag wordt geschaald in 1 GiB-stappen en kan worden geschaald nadat de server is gemaakt.

>[!NOTE]
> Opslag kan alleen omhoog en niet omlaag worden geschaald.

U kunt uw opslag verbruik in de Azure Portal (met Azure Monitor) bewaken met behulp van de opslag limiet, het opslag percentage en de gebruikte metrische gegevens over opslag. Raadpleeg het [controle artikel](./concepts-monitoring.md) voor meer informatie over metrische gegevens. 

### <a name="reaching-the-storage-limit"></a>De opslag limiet wordt bereikt

Wanneer de door de server verbruikte opslag ruimte bijna is bereikt, wordt de server ingesteld op de modus alleen-lezen om de verloren schrijf bewerkingen op de server te beveiligen. Servers die kleiner zijn dan of gelijk zijn aan 100 GiB ingerichte opslag zijn gemarkeerd als alleen-lezen als de beschik bare opslag minder is dan 5% van de ingerichte opslag grootte. Servers met meer dan 100 ingerichte GiB zijn gemarkeerd als alleen-lezen wanneer de vrije opslag minder is dan 5 GiB.

Als u bijvoorbeeld 110 GiB van opslag hebt ingericht en het werkelijke gebruik over 105 GiB gaat, is de server gemarkeerd als alleen-lezen. Als u echter 5 GiB hebt ingericht, is de server gemarkeerd als alleen-lezen wanneer de vrije opslag minder dan 256 MB bedraagt.

Terwijl de service probeert om de server alleen-lezen te maken, worden alle nieuwe transactieaanvragen voor schrijven geblokkeerd en worden bestaande actieve transacties verder uitgevoerd. Indien de server op alleen-lezen is ingesteld, zullen alle daaropvolgende schrijfbewerkingen en transactiedoorvoeringen mislukken. Leesquery’s blijven gewoon werken. 

Als u de server uit de modus alleen-lezen wilt halen, moet u de ingerichte opslag op de server verg Roten. U kunt dit doen met behulp van de Azure Portal of Azure CLI. Zodra de server is toegenomen, is het nu klaar om schrijf transacties te accepteren.

U wordt aangeraden een waarschuwing in te stellen om u te waarschuwen wanneer uw server opslag de drempel waarde nadert, zodat u de alleen-lezen status kunt vermijden. Raadpleeg het [controle artikel](./concepts-monitoring.md) voor meer informatie over de beschik bare metrische gegevens. 

Wij raden u aan <!--turn on storage auto-grow or to--> Stel een waarschuwing in om u te waarschuwen wanneer de server opslag de drempel waarde nadert, zodat u kunt voor komen dat u de status alleen-lezen krijgt. Zie voor meer informatie de documentatie over waarschuwingen over het [instellen van een waarschuwing](how-to-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Opslag automatisch verg Roten

Opslag automatisch uitbreiden is nog niet beschikbaar voor Azure Database for MySQL flexibele server.

## <a name="iops"></a>IOPS
De minimale effectief IOPS is 100 voor alle reken grootten en de Maxi maal bedoel bare IOPS wordt bepaald door de volgende kenmerken: 
- Compute: de Maxi maal beschik bare IOPS kan worden beperkt door de Maxi maal toegestane IOPS van de geselecteerde reken grootte.
- Opslag: in alle reken lagen wordt de schaal van IOPS met de ingerichte opslag grootte in een verhouding van 3:1.

U kunt effectief aantal IOPS beschik bare schalen door de ingerichte opslag te verhogen of te verplaatsen naar een grotere reken grootte (als uw IOPS beperkt zijn door Compute). In de preview-periode is het maximum aantal ondersteunde IOPS 20.000 IOPS.

Zie hieronder voor meer informatie over de Max effectief IOPS per reken grootte met behulp van de combi natie van Compute en opslag: 

| Reken grootte         | Maximum aantal IOPS  | 
|----------------------|---------------------|
| **Bebreekbaar**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **Algemeen doel**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20.000               |
| Standard_D32ds_v4    | 20.000               |
| Standard_D48ds_v4    | 20.000               | 
| Standard_D64ds_v4    | 20.000               | 
| **Geoptimaliseerd voor geheugen** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_ E8ds_v4    | 12800               | 
| Standard_ E16ds_v4   | 20.000               | 
| Standard_E32ds_v4    | 20.000               | 
| Standard_E48ds_v4    | 20.000               | 
| Standard_E64ds_v4    | 20.000               |  

De maximale ingangs limiet is afhankelijk van de Maxi maal beschik bare IOPS per reken grootte. Zie de onderstaande formule en raadpleegt u de kolom Max. niet- *cache geheugen doorvoer: IOPS/Mbps* in de documentatie van de [B-serie](../../virtual-machines/sizes-b-series-burstable.md), [Ddsv4-serie](../../virtual-machines/ddv4-ddsv4-series.md)en [Edsv4-serie](../../virtual-machines/edv4-edsv4-series.md) .

**Max effectief IOPS** = minimum (*"Maxi maal aantal niet-gecachete schijf doorvoer: IOPS/Mbps"* van reken grootte, opslag ingericht in GiB * 3)

U kunt uw I/O-verbruik in de Azure Portal (met Azure Monitor) bewaken met de metrische waarde voor [io-percentage](./concepts-monitoring.md) . Als u meer IOPS nodig hebt, moet u weten of u beperkt bent door de berekenings grootte of de opslag ingericht. Schaal de berekenings-of opslag capaciteit van uw server dienovereenkomstig.

## <a name="backup"></a>Backup

De service maakt automatisch back-ups van uw server. U kunt een Bewaar periode uit een bereik van 1 tot 35 dagen selecteren. Meer informatie over back-ups vindt u in het [artikel back-ups maken en herstellen](concepts-backup-restore.md).

## <a name="scale-resources"></a>Resources schalen

Nadat u de server hebt gemaakt, kunt u de compute-laag, de berekenings grootte (vCores en het geheugen) en de hoeveelheid opslag ruimte en de Bewaar periode voor back-ups afzonderlijk wijzigen. De berekenings grootte kan omhoog of omlaag worden geschaald. De Bewaar periode voor back-ups kan van 1 tot 35 dagen omhoog of omlaag worden geschaald. De opslag grootte kan alleen worden verhoogd. Het schalen van de resources kan worden uitgevoerd via de portal of Azure CLI.

> [!NOTE]
> De opslag grootte kan alleen worden verhoogd. U kunt niet teruggaan naar een kleinere opslag grootte na de verhoging.

Wanneer u de compute-of COMPUTE-grootte wijzigt, wordt de server opnieuw opgestart om het nieuwe server type van kracht te laten worden. Op het moment dat het systeem overschakelt naar de nieuwe server, kunnen er geen nieuwe verbindingen worden vastgelegd en worden alle niet-doorgevoerde transacties teruggedraaid. Dit venster varieert, maar in de meeste gevallen ligt tussen 60-120 seconden. 

Het schalen van de opslag en het wijzigen van de Bewaar periode voor back-ups is online bewerkingen en het opnieuw opstarten van de server is niet vereist.

## <a name="pricing"></a>Prijzen

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/MySQL/)voor services voor de meest actuele prijs informatie. Als u de kosten voor de gewenste configuratie wilt zien, worden in de [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) de maandelijkse kosten weer gegeven op het tabblad **berekening en opslag** op basis van de opties die u selecteert. Als u geen Azure-abonnement hebt, kunt u de Azure-prijs calculator gebruiken om een geschatte prijs te krijgen. Selecteer op de website [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) de optie **items toevoegen**, vouw de categorie **data bases** uit, kies **Azure database for MySQL** en **flexibele server** als implementatie type om de opties aan te passen.

Als u de server kosten wilt optimaliseren, kunt u de volgende tips overwegen:

- Schaal de compute-laag of de reken grootte (vCores) omlaag als Compute is ondergebruikt.
- Overweeg over te scha kelen naar de Burstive Compute-laag als uw workload niet continu de volledige reken capaciteit nodig heeft van de lagen Algemeen en geoptimaliseerd voor geheugen.
- Stop de server wanneer deze niet wordt gebruikt.
- Verminder de Bewaar periode voor back-ups als u geen back-up meer hoeft te bewaren.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een MySQL-server in de portal](quickstart-create-server-portal.md).
- Meer informatie over de beperkingen van de [service](concepts-limitations.md).
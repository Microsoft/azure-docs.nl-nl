---
title: Data afkomst in azure controle sfeer liggen (preview-versie)
description: Hierin worden de concepten voor data afkomst beschreven.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 476355f41de5e0e6aaffdedea8947cab5221767a
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200732"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Data afkomst in azure controle sfeer liggen Data Catalog-client

Dit artikel bevat een overzicht van de gegevens afkomst in azure controle sfeer liggen Data Catalog. Ook wordt beschreven hoe gegevens systemen met de catalogus kunnen worden geïntegreerd om afkomst gegevens vast te leggen. Controle sfeer liggen kan afkomst vastleggen voor gegevens in verschillende delen van de gegevens van uw organisatie en op verschillende manieren van de voor bereiding, waaronder:

- Volledig onbewerkte gegevens die vanaf verschillende platforms worden klaargezet
- Getransformeerde en voor bereide gegevens
- Gegevens die worden gebruikt door visualisatie platforms.

## <a name="use-cases"></a>Gebruiksscenario's

Data afkomst is breed begrepen als de levens cyclus die de oorsprong van de gegevens omvat en waar deze in de loop van de tijd over de gegevens van het onroerend goed wordt verplaatst. Dit wordt gebruikt voor verschillende soorten neerwaartse scenario's, zoals het oplossen van problemen, het traceren van hoofd oorzaken in gegevens pijplijnen en fout opsporing. Afkomst wordt ook gebruikt voor scenario's voor gegevens kwaliteit, naleving en ' What if ' die vaak impact analyse worden genoemd. Afkomst wordt visueel weer gegeven om gegevens weer te geven die worden verplaatst van bron naar bestemming, inclusief de manier waarop de gegevens zijn getransformeerd. Gezien de complexiteit van de meeste bedrijfs gegevens omgevingen, zijn deze weer gaven moeilijk te begrijpen zonder enige samen voeging of maskering van Peripheral data-punten.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Afkomst-ervaring in azure controle sfeer liggen Data Catalog

Controle sfeer liggen Data Catalog maakt verbinding met andere systemen voor gegevens verwerking, opslag en analyse om afkomst-gegevens te extra heren. De gegevens worden gecombineerd om een algemene, scenario-specifieke afkomst-ervaring in de catalogus te vertegenwoordigen.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="end-end afkomst weer gave van gegevens die zijn gekopieerd uit de BLOB Store, de manier waarop ze kunnen Power BI dash board":::

Uw data-onroerend goed kan systemen bevatten die gegevens extractie, trans formatie (ETL/ELT-systemen), analyse en visualisatie systemen uitvoeren. Elk van de systemen legt uitgebreide statische en operationele meta gegevens vast waarmee de status en kwaliteit van de gegevens binnen de systeem grens worden beschreven. Het doel van afkomst in een gegevens catalogus is het extra heren van de beweging, trans formatie en operationele meta gegevens van elk gegevens systeem op het laagste graan mogelijk maakt.

In het volgende voor beeld wordt gebruikgemaakt van gegevens die worden verplaatst over meerdere systemen, waarbij de Data Catalog verbinding zou maken met elk van de systemen voor afkomst.

- Data Factory kopieert gegevens van on-premises/onbewerkte zone naar een landings zone in de Cloud. 
- Gegevensverwerkings systemen zoals Synapse, Databricks verwerken gegevens van de landings zone naar de gehoste zone met behulp van notitie blokken.
- Verdere verwerking van gegevens in analyse modellen voor optimale prestaties en aggregatie van query's. 
- Data visualisatie systemen gebruiken de gegevens sets en het proces via het meta model om een BI-dash board, ML experimenten enzovoort te maken.

## <a name="lineage-granularity"></a>Afkomst granulatie

In deze sectie vindt u informatie over de granulariteit waarvan de afkomst-gegevens worden verzameld door een gegevens catalogus. Deze granulatie kan variëren op basis van de gegevens systemen die worden uitgevoerd.

### <a name="entity-level-lineage-sources--process--targets"></a>Afkomst van entiteits niveau: bron (nen) > proces > doel (en) 

- Afkomst wordt weer gegeven als een grafiek, meestal bevat deze bron-en doel entiteiten in gegevensopslag systemen die zijn verbonden door een proces dat wordt aangeroepen door een berekenings systeem. 
- Gegevens systemen maken verbinding met de Data Catalog om een uniek object te genereren en te rapporteren dat verwijst naar het fysieke object van het onderliggende gegevens systeem bijvoorbeeld: SQL-opgeslagen procedure, notitie blokken, enzovoort.
- Hoge betrouw baarheid van afkomst met aanvullende meta gegevens zoals het eigendom wordt vastgelegd om de afkomst in een lees bare indeling weer te geven voor bron & doel entiteiten. bijvoorbeeld: afkomst op het niveau van een Hive-tabel in plaats van partities of bestands niveau.

### <a name="column-or-attribute-level-lineage"></a>Kolom-of kenmerk niveau afkomst

Identificeer kenmerk (en) van een bron entiteit die wordt gebruikt voor het maken of afleiden van kenmerken in de doel entiteit. De naam van het bron kenmerk kan worden behouden of de naam ervan kan worden gewijzigd in een doel. Systemen zoals ADF kunnen een kopie maken van een on-premises omgeving naar de Cloud. Bijvoorbeeld: `Table1/ColumnA -> Table2/ColumnA`.

### <a name="process-execution-status"></a>Uitvoerings status van proces

Ter ondersteuning van de analyse van de hoofd oorzaak en de gegevens kwaliteit wordt de uitvoerings status van de taken vastgelegd in systemen voor gegevens verwerking. Deze vereiste heeft niets te maken met het vervangen van de bewakings mogelijkheden van andere systemen voor het verwerken van gegevens. het doel is daarom niet te vervangen. 

## <a name="summary"></a>Samenvatting

Afkomst is een belang rijke functie van de controle sfeer liggen-Data Catalog ter ondersteuning van kwaliteits-, vertrouwens-en controle scenario's. Het doel van een Data Catalog is het bouwen van een robuust Framework waarbij alle gegevens systemen in uw omgeving afkomst kunnen maken en rapporteren. Zodra de meta gegevens beschikbaar zijn, kan de Data Catalog de meta gegevens die door gegevens systemen zijn verschaft, samen voegen met Power Data Governance-use cases.

## <a name="next-steps"></a>Volgende stappen

* [Quickstart: Een Azure Purview-account maken in Azure Portal](create-catalog-portal.md)
* [Quickstart: Een Azure Purview-account maken met behulp van Azure PowerShell/Azure CLI](create-catalog-powershell.md)
* [Snelstartgids: controle sfeer liggen Studio gebruiken](use-purview-studio.md)

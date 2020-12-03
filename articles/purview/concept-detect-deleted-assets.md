---
title: Hoe scannen verwijderde assets detecteert
description: In dit artikel wordt uitgelegd hoe een Azure controle sfeer liggen-account verwijderde assets tijdens scans detecteert.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553368"
---
# <a name="how-scans-detect-deleted-assets"></a>Hoe scannen verwijderde assets detecteert

In dit artikel wordt beschreven hoe u in azure controle sfeer liggen scan resultaten gebruikt om verwijderde assets te detecteren.

## <a name="background-info"></a>Achtergrond informatie

Een Azure controle sfeer liggen-catalogus is alleen op de hoogte van de status van een gegevens archief wanneer het wordt gescand. Als u wilt weten of een bestand, tabel of container is verwijderd, vergelijkt de catalogus de laatste scan uitvoer met de huidige Scan uitvoer. Stel bijvoorbeeld dat de laatste keer dat u een Azure Data Lake Storage Gen2 account hebt gescand, een map met de naam *Map1* bevat. Als hetzelfde account opnieuw wordt gescand, ontbreekt *Map1* . De catalogus gaat er daarom van uit dat de map is verwijderd.

## <a name="detecting-deleted-files"></a>Verwijderde bestanden detecteren

De logica voor het detecteren van ontbrekende bestanden werkt voor meerdere scans van dezelfde gebruiker en van verschillende gebruikers. Stel bijvoorbeeld dat een gebruiker een eenmalige scan uitvoert op een Data Lake Storage Gen2 gegevens opslag in de mappen A, B en C. Later voert een andere gebruiker in hetzelfde account een verschillende eenmalige scan uit op mappen C, D en E van hetzelfde gegevens archief. Omdat map C twee keer werd gescand, controleert de catalogus deze op mogelijke verwijderingen. Mappen A, B, D en E zijn echter slechts één keer gescand en de catalogus controleert deze niet op verwijderde assets.

Als u verwijderde bestanden uit uw catalogus wilt verwijderen, is het belang rijk om regel matig scans uit te voeren. Het controle-interval is belang rijk, omdat de catalogus geen verwijderde assets kan detecteren totdat een andere scan wordt uitgevoerd. Als u scans eenmaal per maand op een specifiek archief uitvoert, kan de catalogus geen verwijderde gegevensassets in die Store detecteren totdat u de volgende scan een maand later uitvoert.

Bij het opsommen van grote gegevens archieven zoals Data Lake Storage Gen2, zijn er meerdere manieren (waaronder inventarisatie fouten en verwijderde gebeurtenissen) om informatie te missen. Een bepaalde scan kan missen dat een bestand is gemaakt of verwijderd. Tenzij de catalogus zeker is dat er een bestand is verwijderd, wordt het niet verwijderd uit de catalogus. Deze strategie betekent dat er fouten zijn wanneer een bestand dat niet in het gescande gegevens archief aanwezig is, nog steeds in de catalogus voor komt. In sommige gevallen moet een gegevens archief twee of drie keer worden gescand voordat bepaalde verwijderde assets worden onderschept.

## <a name="next-steps"></a>Volgende stappen

Zie [Quick Start: een controle sfeer liggen-account maken](create-catalog-portal.md)om aan de slag te gaan met Azure controle sfeer liggen Catalogs.

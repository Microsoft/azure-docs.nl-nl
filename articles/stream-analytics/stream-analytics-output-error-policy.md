---
title: Uitvoer fout beleid in Azure Stream Analytics
description: Meer informatie over de beleids regels voor het afhandelen van uitvoer fouten die beschikbaar zijn in Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 19d762a55127af34e84185b11518aa6584acb5bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012407"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Azure Stream Analytics uitvoer fout beleid
In dit artikel worden de beleids regels voor fout afhandeling van uitvoer gegevens beschreven die kunnen worden geconfigureerd in Azure Stream Analytics.

Beleids regels voor fout afhandeling van uitvoer gegevens zijn alleen van toepassing op gegevens conversie fouten die optreden wanneer de uitvoer gebeurtenis die door een Stream Analytics taak wordt geproduceerd, niet overeenkomt met het schema van de doel-sink. U kunt dit beleid configureren door **opnieuw** of **verwijderen** te kiezen. Selecteer in de Azure Portal, terwijl u in een Stream Analytics-taak, onder **configureren**, **fout beleid** om uw selectie te maken.

![Locatie van Azure Stream Analytics voor uitvoer fout beleid](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Opnieuw proberen
Als er een fout optreedt, wordt Azure Stream Analytics de gebeurtenis voor onbepaalde tijd opnieuw geprobeerd tot de schrijf bewerking slaagt. Er is geen time-out voor nieuwe pogingen. Uiteindelijk zijn alle volgende gebeurtenissen geblokkeerd voor verwerking door de gebeurtenis die opnieuw wordt geprobeerd. Deze optie is het standaard beleid voor het afhandelen van uitvoer fouten.

## <a name="drop"></a>Directe
Door Azure Stream Analytics worden uitvoergebeurtenissen die tot een gegevensconversiefout leiden verwijderd. De verwijderde gebeurtenissen kunnen niet worden hersteld voor latere verwerking.


Alle tijdelijke fouten (bijvoorbeeld netwerk fouten) worden opnieuw geprobeerd, ongeacht de configuratie van het afhandelings beleid voor uitvoer fouten.


## <a name="next-steps"></a>Volgende stappen
[Gids voor probleem oplossing voor Azure Stream Analytics](./stream-analytics-troubleshoot-query.md)
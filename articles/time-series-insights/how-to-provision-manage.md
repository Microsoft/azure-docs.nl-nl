---
title: Een Azure-omgeving voor meerdere bedrijfs omgevingen beheren-tijd reeks | Microsoft Docs
description: Meer informatie over het beheren van een Azure Time Series Insights-omgeving van 2.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: cbd28bdf5318bdaf932447f5d1f936d2c614a7f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461892"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2 beheren

Nadat u uw Azure Time Series Insights Gen2-omgeving hebt gemaakt met behulp van [de Azure cli](./how-to-create-environment-using-cli.md) of [de Azure Portal](./how-to-create-environment-using-portal.md), kunt u uw toegangs beleid en andere omgevings kenmerken aanpassen aan uw bedrijfs behoeften.

## <a name="manage-the-environment"></a>De omgeving beheren

U kunt uw Azure Time Series Insights Gen2-omgeving beheren door gebruik te maken van de [Azure Portal](https://portal.azure.com/). Er zijn enkele belang rijke verschillen tussen een Gen2-omgeving en gen1 S1-of gen1 S2-omgevingen waar u rekening mee moet doen wanneer u uw omgeving beheert via de Azure Portal:

* Het **overzichts** venster van de Azure Portal Gen2 heeft de volgende wijzigingen:

  * De capaciteit wordt verwijderd omdat deze niet van toepassing is op Gen2-omgevingen.
  * De eigenschap **Time Series id** wordt toegevoegd. Hiermee wordt bepaald hoe uw gegevens worden gepartitioneerd.
  * Verwijzings gegevens sets worden verwijderd.
  * Met de weer gegeven URL wordt u omgeleid naar de [Azure time series Insights Explorer](./concepts-ux-panels.md).
  * De naam van uw Azure Storage-account wordt vermeld.

* Het deel venster **configuratie** van de Azure portal wordt verwijderd omdat schaal eenheden niet van toepassing zijn op Azure time series Insights Gen2-omgevingen. U kunt echter **opslag configuratie** gebruiken om de zojuist geïntroduceerde warme Store te configureren.

* Het deel venster met **referentie gegevens** van de Azure portal wordt verwijderd uit Azure time series Insights Gen2 omdat referentie gegevens concept is vervangen door [TSM (Time Series model)](./concepts-model-overview.md).

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Azure Time Series Insights Gen2-omgeving in de Azure Portal":::

## <a name="next-steps"></a>Volgende stappen

* Bekijk de lijst met [Aanbevolen procedures voor streaming-opname](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)
* Meer informatie over het [vaststellen en oplossen van problemen](./how-to-diagnose-troubleshoot.md)

---
title: Live gebeurtenis statussen en facturering in Azure Media Services
description: Dit onderwerp bevat een overzicht van Azure Media Services statussen en facturering van Live-gebeurtenissen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: c9fa12e1ee3778d0865c75662064bd4067e56d89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897812"
---
# <a name="live-event-states-and-billing"></a>Live gebeurtenis statussen en facturering

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Azure Media Services begint de facturering van een live-gebeurtenis zodra de status verandert in **actief** of **stand-by**. U wordt gefactureerd, zelfs als er geen video door de service wordt getransporteerd. Als u de live-gebeurtenis van facturering wilt stoppen, moet u de live-gebeurtenis beëindigen. Live transcriptie wordt gefactureerd op dezelfde manier als de live-gebeurtenis.

Wanneer **LiveEventEncodingType** voor uw [live-gebeurtenis](/rest/api/media/liveevents) is ingesteld op Standard of Premium1080p, wordt met Media Services automatisch elke live gebeurtenis afgesloten die nog 12 uur na het invoeren van de invoer wordt **uitgevoerd** , en worden er geen **Live uitvoer** s uitgevoerd. Er worden echter nog steeds kosten in rekening gebracht voor de tijd dat de live-gebeurtenis de status **actief** heeft.

> [!NOTE]
> Passthrough-Live-gebeurtenissen worden niet automatisch afgesloten en moeten expliciet worden gestopt door de API om buitensporige facturering te voor komen.

## <a name="states"></a>Staten

De live-gebeurtenis kan een van de volgende statussen hebben.

|Staat|Beschrijving|
|---|---|
|**Gestopt**| Dit is de begin status van de live gebeurtenis na het maken (tenzij auto start is ingesteld op True.) Er vindt geen facturering plaats in deze status. Er kan geen invoer worden ontvangen door de live-gebeurtenis. |
|**Starten**| De live gebeurtenis wordt gestart en resources worden toegewezen. Er vindt geen facturering plaats in deze status.  Als er een fout optreedt, keert de live-gebeurtenis terug naar de status gestopt.|
| **Toewijzen** | De toewijzings actie is aangeroepen voor de live gebeurtenis en de resources worden ingericht voor deze live-gebeurtenis. Zodra deze bewerking is voltooid, wordt de status van de live-gebeurtenis gewijzigd in stand-by.
|**Modus**| Live Event-resources zijn ingericht en zijn klaar om te beginnen. Facturering vindt plaats in deze status.  De meeste eigenschappen kunnen nog steeds worden bijgewerkt, maar opname of streaming is niet toegestaan tijdens deze status.
|**Wordt uitgevoerd**| De live-gebeurtenis resources zijn toegewezen, opname-en preview-Url's zijn gegenereerd en kunnen live streams ontvangen. Op dit moment is facturering actief. U moet stoppen op de resource van de live-gebeurtenis expliciet aanroepen om verdere facturering te stoppen.|
|**Stoppen**| De live gebeurtenis wordt gestopt en de inrichting van de resources wordt ongedaan gemaakt. Er vindt geen facturering plaats in deze tijdelijke status. |
|**Verwijderen**| De live-gebeurtenis wordt verwijderd. Er vindt geen facturering plaats in deze tijdelijke status. |

U kunt ervoor kiezen om live-transcripties in te scha kelen wanneer u de live-gebeurtenis maakt. Als u dit doet, wordt u gefactureerd voor Live transcripties wanneer de live-gebeurtenis de status **actief** heeft. Houd er rekening mee dat u wordt gefactureerd, zelfs als er geen audio stroom door de live-gebeurtenis plaatsvindt.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van live streamen](live-streaming-overview.md)
- [Zelf studie over live streamen](stream-live-tutorial-with-api.md)

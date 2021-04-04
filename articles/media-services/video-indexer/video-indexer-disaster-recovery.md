---
title: Failover en herstel na noodgeval in Video Indexer
titleSuffix: Azure Media Services
description: Meer informatie over hoe u een failover naar een secundaire Video Indexer-account kunt doen als er een fout optreedt in een regionaal Data Center of nood geval.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: eab376c44065979de86e5c70b796be952fccffaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87065405"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Failover en herstel na noodgeval in Video Indexer

Azure Media Services Video Indexer biedt geen onmiddellijke failover van de service als er sprake is van een storing of fout in het regionale datacentrum. In dit artikel wordt uitgelegd hoe u uw omgeving kunt configureren voor een failover om optimale Beschik baarheid voor apps en een geminimaliseerd herstel tijdstip te garanderen als er sprake is van een nood geval.

We raden u aan om BCDR (bedrijfscontinuïteit en herstel na noodgevallen) te configureren voor regionale paren, om te profiteren van het beleid voor isolatie en beschikbaarheid in Azure. Zie [Gekoppelde Azure-regio's](../../best-practices-availability-paired-regions.md) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, meldt u zich aan voor een [gratis proef versie van Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Failover naar een secundair account

Voor het implementeren van BCDR moet u twee Video Indexer accounts hebben om redundantie te kunnen afhandelen.

1. Maak twee Video Indexer accounts die zijn verbonden met Azure (Zie [een video indexer-account maken](connect-to-azure.md)). Maak een account voor uw primaire regio en de andere voor de gekoppelde Azure-regio.
1. Als er een fout is opgetreden in de primaire regio, schakelt u over naar indexeren met het secundaire account.

> [!TIP]
> U kunt BCDR automatiseren door waarschuwingen voor activiteiten Logboeken in te stellen voor service status meldingen per [activiteit logboek waarschuwingen maken voor service meldingen](../../service-health/alerts-activity-log-service-notifications-portal.md).

Zie [meerdere tenants beheren](manage-multiple-tenants.md)voor meer informatie over het gebruik van meerdere tenants. Als u BCDR wilt implementeren, kiest u een van de volgende twee opties: [video indexer account per Tenant](manage-multiple-tenants.md#video-indexer-account-per-tenant) of [Azure-abonnement per Tenant](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Volgende stappen

[Een video indexer account beheren dat is verbonden met Azure](manage-account-connected-to-azure.md).

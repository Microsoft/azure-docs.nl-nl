---
title: Een Internet domein naar Traffic Manager-Azure-Traffic Manager verwijzen
description: Aan de hand van dit artikel kunt u een domeinnaam van uw bedrijf laten wijzen naar een Traffic Manager-domeinnaam.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: duau
ms.openlocfilehash: 93a8076f12b8f006d600cbd32ce39169f4b47c67
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014326"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Het internetdomein van een bedrijf naar een Traffic Manager-domein laten wijzen

Wanneer u een Traffic Manager-profiel maakt, wijst Azure automatisch een DNS-naam toe voor dat profiel. Om een naam voor de DNS-zone te gebruiken, maakt u een DNS CNAME-record die wordt toegewezen aan de domeinnaam van uw Traffic Manager-profiel. U vindt de Traffic Manager-domeinnaam in het gedeelte **Algemeen** van de configuratiepagina van het Traffic Manager-profiel.

Om de naam `www.contoso.com` bijvoorbeeld te laten verwijzen naar de Traffic Manager-DNS-naam `contoso.trafficmanager.net`, maakt u de volgende DNS-bronrecord:

`www.contoso.com IN CNAME contoso.trafficmanager.net.`

Alle verkeers aanvragen naar *www- \. contoso.com* worden doorgestuurd naar *contoso.trafficmanager.net*.

> [!IMPORTANT]
> U kunt niet naar domeinen op het tweede niveau wijzen, zoals *contoso.com* op het Traffic Manager-domein. DNS-protocolstandaarden staan geen CNAME-records toe voor domeinnamen van het tweede niveau.

## <a name="next-steps"></a>Volgende stappen

* [Methoden voor het doorsturen van Traffic Manager](traffic-manager-routing-methods.md)
* [Traffic Manager - Een profiel uitschakelen, inschakelen of verwijderen](disable-enable-or-delete-a-profile.md)
* [Traffic Manager - Een eindpunt in- of uitschakelen](disable-or-enable-an-endpoint.md)

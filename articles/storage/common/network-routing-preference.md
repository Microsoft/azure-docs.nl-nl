---
title: Voorkeurs netwerk routering
titleSuffix: Azure Storage
description: Met voorkeurs instellingen voor netwerk routering kunt u opgeven hoe netwerk verkeer via internet wordt doorgestuurd naar uw account van clients.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: bf2270fe6f71dfe5be31db8e82f6c44696f28074
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601488"
---
# <a name="network-routing-preference-for-azure-storage"></a>Voor keur voor netwerk routering voor Azure Storage

U kunt netwerk [Routering](../../virtual-network/routing-preference-overview.md) voor uw Azure Storage-account configureren om op te geven hoe netwerk verkeer via internet naar uw account wordt doorgestuurd. Verkeer van Internet wordt standaard doorgestuurd naar het open bare eind punt van uw opslag account via het [globale micro soft-netwerk](../../networking/microsoft-global-network.md). Azure Storage biedt aanvullende opties voor het configureren van de manier waarop verkeer wordt gerouteerd naar uw opslag account.

Door routerings voorkeur te configureren, hebt u de flexibiliteit om uw verkeer te optimaliseren voor Premium-netwerk prestaties of voor kosten. Wanneer u een routerings voorkeur configureert, geeft u op hoe verkeer standaard wordt omgeleid naar het open bare eind punt voor uw opslag account. U kunt ook route-specifieke eind punten voor uw opslag account publiceren.

> [!NOTE]
> Deze functie wordt niet ondersteund in opslag accounts die zijn geconfigureerd voor gebruik van de Premium-prestatie tier of zone-redundante opslag (ZRS).

## <a name="microsoft-global-network-versus-internet-routing"></a>Micro soft wereld wijd netwerk versus Internet Routing

Clients buiten de Azure-omgeving hebben standaard toegang tot uw opslag account via het wereld wijde netwerk van micro soft. Het wereld wijde netwerk van micro soft is geoptimaliseerd voor het selecteren van een laag latentie om Premium-netwerk prestaties te leveren met hoge betrouw baarheid. Zowel binnenkomend als uitgaand verkeer wordt doorgestuurd via de POP (Point of Presence) die het dichtst bij de client staat. Deze standaard routerings configuratie zorgt ervoor dat verkeer van en naar uw opslag account via het wereld wijde micro soft-netwerk voor het meren deel van het pad wordt gemaximaliseerd, waardoor het netwerk niet optimaal presteert.

U kunt de routerings configuratie voor uw opslag account wijzigen zodat zowel binnenkomend als uitgaand verkeer wordt doorgestuurd van en naar clients buiten de Azure-omgeving via de POP die het dichtst bij het opslag account ligt. Met deze route wordt het passeren van uw verkeer via het wereld wijde micro soft-netwerk geminimaliseerd. Het gebruik van deze routerings configuratie verlaagt de netwerk kosten.

In het volgende diagram ziet u hoe verkeer loopt tussen de client en het opslag account voor elke routerings voorkeur:

![Overzicht van routerings opties voor Azure Storage](media/network-routing-preference/routing-options-diagram.png)

Zie [Wat is routerings voorkeur?](../../virtual-network/routing-preference-overview.md)voor meer informatie over routerings voorkeur in Azure.

## <a name="routing-configuration"></a>Routerings configuratie

Zie [netwerk routering configureren voor Azure Storage](configure-network-routing-preference.md)voor stapsgewijze instructies voor het configureren van de routerings voorkeur en de route-specifieke eind punten.

U kunt kiezen tussen het globale netwerk van micro soft en Internet routering als de standaard routerings voorkeur voor het open bare eind punt van uw opslag account. De standaard routerings voorkeur is van toepassing op alle verkeer van clients buiten Azure en is van invloed op de eind punten voor Azure Data Lake Storage Gen2, Blob Storage, Azure Files en statische websites. Het configureren van de routerings voorkeur wordt niet ondersteund voor Azure-wacht rijen of Azure-tabellen.

U kunt ook route-specifieke eind punten voor uw opslag account publiceren. Wanneer u route-specifieke eind punten publiceert, maakt Azure Storage nieuwe open bare eind punten voor uw opslag account die verkeer via het gewenste pad routeren. Met deze flexibiliteit kunt u verkeer naar uw opslag account omleiden via een specifieke route zonder uw standaard routerings voorkeur te wijzigen.

Als u bijvoorbeeld een Internet route-specifiek eind punt voor de ' StorageAccountA ' publiceert, worden de volgende eind punten voor uw opslag account gepubliceerd:

| Opslag service        | Route-specifiek eind punt                                  |
| :--------------------- | :------------------------------------------------------- |
| Blob-service           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| Bestandsservice           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| Statische websites        | `StorageAccountA-internetrouting.web.core.windows.net`   |

Als u een geografisch redundante opslag met lees toegang (RA-GRS) of een opslag account met geozone-redundante opslag met lees toegang (RA-GZRS) hebt, worden de bijbehorende eind punten in de secundaire regio ook automatisch gemaakt voor lees toegang.

| Opslag service        | Route-specifiek secundair eind punt voor alleen-lezen                        |
| :--------------------- | :----------------------------------------------------------------- |
| Blob-service           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| Bestandsservice           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| Statische websites        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

De verbindings reeksen voor de gepubliceerde route-specifieke eind punten kunnen worden gekopieerd via de [Azure Portal](https://portal.azure.com). Deze verbindings reeksen kunnen worden gebruikt voor gedeelde sleutel autorisatie met alle bestaande Azure Storage Sdk's en Api's.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Routerings voorkeur voor Azure Storage is beschikbaar in de volgende regio's:

- VS - centraal 
- VS - centraal EUAP
- VS - oost 
- VS - oost 2
- VS - oost 2 
- VS-Oost 2 EUAP
- VS - zuid-centraal
- VS - west-centraal
- VS - west 
- VS - west 2 
- Frankrijk - centraal 
- Frankrijk - zuid 
- Duitsland - noord 
- Duitsland - west-centraal 
- VS - noord-centraal
- Europa - noord 
- Noorwegen - oost 
- Zwitserland - noord
- Zwitserland - west
- Verenigd Koninkrijk Zuid 
- Verenigd Koninkrijk West 
- Europa -west 
- UAE - centraal
- Azië - oost 
- Azië - zuidoost 
- Japan - oost 
- Japan - west 
- India - west
- Australië - oost 
- Australië - zuidoost 

De volgende bekende problemen zijn van invloed op de routerings voorkeur voor Azure Storage:

- Toegangs aanvragen voor het route-specifieke eind punt voor het micro soft Global Network mislukken met HTTP-fout 404 of een gelijkwaardige waarde. Route ring via het globale micro soft-netwerk werkt zoals verwacht wanneer het is ingesteld als de standaard routerings voorkeur voor het open bare eind punt.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Zie de sectie **prijzen** in [Wat is routerings voorkeur?](../../virtual-network/routing-preference-overview.md#pricing)voor prijzen en facturerings gegevens.

## <a name="next-steps"></a>Volgende stappen

- [Wat is een routeringsvoorkeur?](../../virtual-network/routing-preference-overview.md)
- [Netwerkrouternigsvoorkeur configureren](configure-network-routing-preference.md)
- [Azure Storage-firewalls en virtuele netwerken configureren](storage-network-security.md)
- [Beveiligings aanbevelingen voor Blob Storage](../blobs/security-recommendations.md)

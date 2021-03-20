---
title: Meerdere tenants beheren met Video Indexer-Azure
description: In dit artikel worden verschillende integratie opties voor het beheren van meerdere tenants met Video Indexer voorgesteld.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 18f2cf3daa281400151ba223e1735e7138d97e8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76990501"
---
# <a name="manage-multiple-tenants"></a>Meerdere tenants beheren

In dit artikel worden verschillende opties beschreven voor het beheren van meerdere tenants met Video Indexer. Kies een methode die het meest geschikt is voor uw scenario:

* Video Indexer-account per tenant
* Eén Video Indexer-account voor alle tenants
* Azure-abonnement per tenant

## <a name="video-indexer-account-per-tenant"></a>Video Indexer-account per tenant

Wanneer u deze architectuur gebruikt, wordt er voor elke Tenant een Video Indexer-account gemaakt. De tenants hebben een volledige isolatie in de permanente en Compute-laag.  

![Video Indexer-account per tenant](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Overwegingen

* Klanten delen geen opslag accounts (tenzij ze hand matig zijn geconfigureerd door de klant).
* Klanten delen geen reken capaciteit (gereserveerde eenheden) en hebben geen invloed op de verwerkings taken van elkaar.
* U kunt eenvoudig een Tenant verwijderen uit het systeem door het Video Indexer-account te verwijderen.
* Het is niet mogelijk om aangepaste modellen te delen tussen tenants.

    Zorg ervoor dat er geen zakelijke vereiste is om aangepaste modellen te delen.
* Moeilijker te beheren vanwege meerdere Video Indexer (en gekoppelde Media Services) accounts per Tenant.

> [!TIP]
> Maak een gebruiker met beheerders rechten voor uw systeem in [video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/) en gebruik de autorisatie-API om uw tenants het relevante [account toegangs token](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token)te geven.

## <a name="single-video-indexer-account-for-all-users"></a>Eén Video Indexer account voor alle gebruikers

Wanneer deze architectuur wordt gebruikt, is de klant verantwoordelijk voor de isolatie van tenants. Alle tenants moeten één Video Indexer account gebruiken met één Azure media service-account. Wanneer u inhoud uploadt, zoekt of verwijdert, moet de klant de juiste resultaten voor die Tenant filteren.

![Eén Video Indexer account voor alle gebruikers](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Met deze optie kunnen aanpassings modellen (persoon, taal en merken) worden gedeeld of geïsoleerd tussen tenants door de modellen te filteren op Tenant.

Wanneer u [Video's uploadt](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?), kunt u een ander partitie kenmerk per Tenant opgeven. Hiermee wordt isolatie in de [zoek-API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?)toegestaan. Door het partitie kenmerk in de zoek-API op te geven, krijgt u alleen resultaten van de opgegeven partitie. 

### <a name="considerations"></a>Overwegingen

* De mogelijkheid om inhoud en aanpassings modellen te delen tussen tenants.
* Eén Tenant heeft invloed op de prestaties van andere tenants.
* De klant moet een complexe Management-laag op Video Indexer bouwen.

> [!TIP]
> U kunt het kenmerk [Priority](upload-index-videos.md) gebruiken om taken voor tenants prioriteit te geven.

## <a name="azure-subscription-per-tenant"></a>Azure-abonnement per tenant 

Wanneer u deze architectuur gebruikt, heeft elke Tenant hun eigen Azure-abonnement. Voor elke gebruiker maakt u een nieuw Video Indexer-account in het Tenant abonnement.

![Azure-abonnement per tenant](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Overwegingen

* Dit is de enige optie voor het inschakelen van factuur scheiding.
* Deze integratie heeft meer beheer overhead dan Video Indexer account per Tenant. Als facturering geen vereiste is, kunt u het beste een van de andere opties gebruiken die in dit artikel worden beschreven.

## <a name="next-steps"></a>Volgende stappen

[Overzicht](video-indexer-overview.md)

---
title: Routerings voorkeur niet in azure-data limiet
description: Meer informatie over hoe u routerings voorkeur voor uw resources egressing-gegevens kunt configureren voor de CDN-provider.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about enabling routing preference for my CDN origin resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 1fcc918e5b4b54a415fed0f38d210aeeaa62c008
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679790"
---
# <a name="what-is-routing-preference-unmetered"></a>Wat is de routerings voorkeur undataing?

Er is een routerings voorkeur zonder data limiet beschikbaar voor Content Delivery Network CDN-providers met klanten die hun oorspronkelijke inhoud in azure hosten. Met de service kunnen CDN-providers direct peering-verbinding maken met de micro soft Global Network-rand routers op verschillende locaties.

![Routerings voorkeur niet in data limiet](media/routing-preference-unmetered/unmetered.png)

Uw netwerk verkeer egressing van oorsprong in azure dat is bestemd voor de voor delen van de CDN-provider van de directe connectiviteit.
* De factuur voor gegevens overdracht voor verkeer egressing van uw Azure-resources die via deze rechtstreekse koppelingen worden doorgestuurd, is gratis.
* Directe verbinding tussen CDN-provider en oorsprong in Azure biedt optimale prestaties omdat er geen hops tussen zijn. Dit biedt voor delen van de CDN-werk belasting die regel matig gegevens ophaalt uit de oorsprong.

## <a name="configuring-routing-preference-unmetered"></a>Routerings voorkeur configureren niet in data limiet

Uw CDN-providers moeten deel uitmaken van dit programma om gebruik te kunnen maken van routerings voorkeur zonder data limiet. Als uw CDN-provider geen deel uitmaakt van het programma, neemt u contact op met uw CDN-provider.

Configureer vervolgens routerings voorkeur voor uw resources en stel het voorkeurs type route ring in op **Internet**. U kunt routerings voorkeur configureren tijdens het maken van een openbaar IP-adres en vervolgens de open bare IP koppelen aan resources zoals virtuele machines, Internet gerichte load balancers en nog veel meer. [Meer informatie over het configureren van routerings voorkeur voor een openbaar IP-adres met behulp van de Azure Portal](routing-preference-portal.md)

U kunt ook routerings voorkeur inschakelen voor uw opslag account en een tweede eind punt publiceren dat moet worden gebruikt door de CDN-provider om gegevens op te halen uit de opslag oorsprong. Als u bijvoorbeeld een specifiek *StorageAccountA* -eind punt voor het opslag account publiceert, wordt het tweede eind punt voor uw opslag Services gepubliceerd, zoals hieronder wordt weer gegeven:

![Routerings voorkeur voor opslag accounts](media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>Volgende stappen

* [Routerings voorkeur configureren voor een virtuele machine met behulp van de Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Routerings voorkeur configureren voor een virtuele machine met behulp van Azure CLI](configure-routing-preference-virtual-machine-cli.md)
* [Routerings voorkeur voor uw opslag account configureren](/azure/storage/common/network-routing-preference)
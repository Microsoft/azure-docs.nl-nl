---
title: Azure peering service inschakelen op een directe peering met behulp van de Azure Portal
titleSuffix: Azure
description: Azure peering service inschakelen op een directe peering met behulp van de Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: a52e6038b622c004dc0d133394cd4f53600b2935
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84700039"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Azure peering service inschakelen op een directe peering met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u Azure [peering service](overview-peering-service.md) kunt inschakelen op een directe peering met behulp van de Azure Portal.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van [Power shell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Voordat u begint
* Controleer de [vereisten](prerequisites.md) voordat u begint met de configuratie.
* Kies een directe peering in uw abonnement waarvoor u de peering-service wilt inschakelen. Als u er nog geen hebt, moet u een verouderde directe peering converteren of een nieuwe directe peering maken:
    * Als u een verouderde directe peering wilt converteren, volgt u de instructies in [een verouderde directe peering converteren naar een Azure-resource met behulp van de portal](howto-legacy-direct-portal.md).
    * Als u een nieuwe directe peering wilt maken, volgt u de instructies in [een directe peering maken of wijzigen met behulp van de portal](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Peering Service inschakelen op Direct peering

### <a name="view-direct-peering"></a><a name= get></a>Directe peering weer geven
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>De directe peering inschakelen voor de peering-service

Nadat u in de vorige stap een directe peering hebt geopend, schakelt u deze in voor de peering-service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Een directe peering-verbinding wijzigen

Als u de verbindings instellingen wilt wijzigen, raadpleegt u de sectie ' een directe peering wijzigen ' in [een directe peering maken of wijzigen met behulp van de portal](howto-direct-portal.md).

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
* [Een verouderde Exchange-peering converteren naar een Azure-resource met behulp van de portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [Veelgestelde vragen over peering-service](service-faqs.md)voor meer informatie.
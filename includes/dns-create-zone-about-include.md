---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 74031a8dbc9b64d6a09533789eed1296ff334d47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176696"
---
Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Als u uw domein wilt hosten in Azure DNS, moet u een DNS-zone maken voor die domeinnaam. Alle DNS-records voor uw domein worden vervolgens gemaakt binnen deze DNS-zone.

Het domein contoso.com kan bijvoorbeeld een aantal DNS-records bevatten, zoals mail.contoso.com (voor een e-mailserver) en www.contoso.com (voor een website).

Bij het maken van een DNS-zone in Azure DNS:

* De naam van de zone moet uniek zijn binnen de resourcegroep en de zone mag niet al bestaan. Anders mislukt de bewerking.
* Dezelfde zonenaam kan opnieuw worden gebruikt in een andere resourcegroep of in een ander Azure-abonnement.
* Als meerdere zones dezelfde naam delen, wordt aan elk exemplaar een ander naamserveradres toegewezen. Er kan bij de domeinnaamregistrar slechts één set adressen worden geconfigureerd.

> [!NOTE]
> U hoeft niet de eigenaar van een domeinnaam te zijn om een DNS-zone met die domeinnaam in Azure DNS te maken. U moet echter wel de eigenaar van het domein zijn om de Azure DNS-naamservers bij de domeinnaamregistrar te configureren als de juiste naamservers voor de domeinnaam.
> 
> Zie [Delegate a domain to Azure DNS](../articles/dns/dns-domain-delegation.md) (Een domein aan Azure DNS overdragen) voor meer informatie.
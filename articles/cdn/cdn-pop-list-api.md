---
title: De huidige POP-IP-lijst voor Azure CDN ophalen | Microsoft Docs
description: Meer informatie over het ophalen van POP-servers met behulp van de REST API. POP-servers maken aanvragen voor oorspronkelijke servers die zijn gekoppeld aan Azure Content Delivery Network-eind punten.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 4197b1a5f047190872d055dc2ba8ccaa11efbe6c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376340"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>De huidige POP-IP-lijst voor Azure CDN ophalen

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>De huidige Verizon POP IP-lijst voor Azure CDN ophalen

U kunt de REST API gebruiken om de set IP-adressen op te halen voor de POP-servers (Point of Presence) van Verizon. Deze POP-servers maken aanvragen voor de oorspronkelijke servers die zijn gekoppeld aan de Azure Content Delivery Network (CDN)-eind punten op een Verizon-Profiel (**Azure CDN Standard van Verizon** of **Azure CDN Premium van Verizon**). Houd er rekening mee dat deze set IP-adressen verschilt van de IP-adressen die een client zou zien wanneer ze aanvragen indienen bij de Pop's. 

Zie [Edge nodes-List](/rest/api/cdn/cdn/edgenodes/list)(Engelstalig) voor de syntaxis van de rest API bewerking voor het ophalen van de POP-lijst.

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>De huidige micro soft POP-IP-lijst voor Azure CDN ophalen

Als u uw toepassing wilt vergren delen zodat alleen verkeer van Azure CDN van micro soft wordt geaccepteerd, moet u IP-Acl's instellen voor uw back-end. U kunt ook de set geaccepteerde waarden voor de header X-forward-host die door Azure CDN van micro soft is verzonden, beperken. Deze stappen worden hieronder beschreven:

Configureer IP-Acl's voor voor uw back-ends om verkeer te accepteren van Azure CDN van de back-end-IP-adres ruimte van micro soft en alleen de infrastructuur services van Azure. 

* Azure CDN van de IPv4-back-end-IP-ruimte van micro soft: 147.243.0.0/16
* Azure CDN van de IPv6-back-end-IP-ruimte van micro soft: 2a01:111:2050::/44

Als u service tags met Azure CDN van micro soft wilt gebruiken, moet u de Azure front-deur label gebruiken. IP-adresbereiken en service tags voor micro soft-Services vindt u [hier](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Typische gebruiksscenario's

Uit veiligheids overwegingen kunt u deze IP-lijst gebruiken om af te dwingen dat aanvragen naar uw oorspronkelijke server alleen worden gemaakt op basis van een geldige Verizon-POP. Als iemand bijvoorbeeld de hostnaam of het IP-adres voor de oorspronkelijke server van een CDN-eind punt heeft gedetecteerd, kan de aanvraag rechtstreeks naar de oorspronkelijke server worden uitgevoerd, waardoor de schaal-en beveiligings mogelijkheden van Azure CDN worden omzeild. Dit scenario kan worden voor komen door de IP-adressen in de geretourneerde lijst als de enige toegestane IP-adressen op een bron server in te stellen. Om ervoor te zorgen dat u de meest recente POP-lijst hebt, moet u deze mini maal één keer per dag ophalen. 

## <a name="next-steps"></a>Volgende stappen

Zie [Azure CDN rest API](/rest/api/cdn/)voor meer informatie over de rest API.
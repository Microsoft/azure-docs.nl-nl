---
title: 'Voor beeld-API management-beleid: filteren op IP-adres bij gebruik van Application Gateway'
titleSuffix: Azure API Management
description: 'Voor beeld van Azure API management-beleid: demonstreert hoe u het IP-adres van de aanvraag filtert bij het gebruik van een Application Gateway.'
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 8249cc543c6334841c8e5152d5d1ceb84d4097dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076110"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filteren op IP-adres van aanvraag bij het gebruik van een Application Gateway

In dit artikel wordt een voor beeld van een Azure API management-beleid weer gegeven waarin wordt getoond hoe u het IP-adres van de aanvraag filtert wanneer het API Management exemplaar wordt geopend via een Application Gateway of een andere intermediair. Volg de stappen die worden beschreven in [een beleid instellen of bewerken](../set-edit-policies.md)om een beleids code in te stellen of te bewerken. Zie voor andere voor beelden [beleids voorbeelden](../policy-reference.md).

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Beleid voor toegangs beperkingen](../api-management-access-restriction-policies.md)
+ [Voorbeelden van beleid](../policy-reference.md)
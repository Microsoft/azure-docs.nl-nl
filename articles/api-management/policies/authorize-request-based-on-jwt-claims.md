---
title: Voor beeld van API management-beleid-toegang verlenen op basis van JWT-claims
titleSuffix: Azure API Management
description: 'Voor beeld van Azure API management-beleid: demonstreert hoe u toegang kunt verlenen tot specifieke HTTP-methoden voor een API op basis van JWT-claims.'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: b5b0c2feb31eab5e39bbebd7108b3ec8504769d4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92076280"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Toegang verlenen op basis van JWT-claims 

Dit artikel bevat een voor beeld van een Azure API management-beleid dat laat zien hoe u toegang tot specifieke HTTP-methoden voor een API op basis van JWT-claims kunt autoriseren. Volg de stappen die worden beschreven in [een beleid instellen of bewerken](../set-edit-policies.md)om een beleids code in te stellen of te bewerken. Zie voor andere voor beelden [beleids voorbeelden](../policy-reference.md).

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Voorbeelden van beleid](../policy-reference.md)
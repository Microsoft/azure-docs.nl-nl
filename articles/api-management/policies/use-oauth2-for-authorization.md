---
title: 'Voor beeld van Azure API management-beleid: gebruik OAuth2 voor autorisatie tussen de gateway en de back-end'
titleSuffix: Azure API Management
description: 'Voor beeld van Azure API management-beleid: demonstreert hoe u OAuth2 kunt gebruiken voor autorisatie tussen de gateway en een back-end. Er wordt weergegeven hoe u een toegangstoken van AAD kunt verkrijgen en dit door kunt sturen naar de back-end.'
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
ms.openlocfilehash: 0f5a10eb2ebc3b3a7c527dd718e37faf03c927bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076093"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>OAuth2 gebruiken voor autorisatie tussen de gateway en een back-end

In dit artikel wordt een voor beeld van een Azure API management-beleid weer gegeven waarin wordt getoond hoe u OAuth2 kunt gebruiken voor autorisatie tussen de gateway en een back-end. Er wordt weergegeven hoe u een toegangstoken van AAD kunt verkrijgen en dit door kunt sturen naar de back-end. 

Volg de stappen die worden beschreven in [een beleid instellen of bewerken](../set-edit-policies.md)om een beleids code in te stellen of te bewerken. Zie voor andere voor beelden [beleids voorbeelden](../policy-reference.md).

Het volgende script maakt gebruik van eigenschappen die in {{Property}} worden weer gegeven. Zie [Dit](../api-management-howto-properties.md) onderwerp voor meer informatie over eigenschappen en hoe u deze in API management-beleid kunt gebruiken.
 
## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Voorbeelden van beleid](../policy-reference.md)
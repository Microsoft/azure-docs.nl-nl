---
title: Protocollen en code ringen beheren in azure API Management | Microsoft Docs
description: Meer informatie over het beheren van protocollen (TLS) en code ringen (DES) in azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 043a3d0b63dfc74f587b58b3c2ac42f1a084cc4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86250308"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Protocollen en ciphers beheren in Azure API Management

Azure API Management ondersteunt meerdere versies van het TLS-protocol voor zowel client-als back-endservers en de 3DES-code ring.

In deze hand leiding wordt beschreven hoe u protocollen en coderings configuratie beheert voor een Azure API Management-exemplaar.

![Protocollen en code ringen in APIM beheren](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt volgen, hebt u het volgende nodig:

* Een API Management-exemplaar

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>TLS-protocollen en 3DES-code ring beheren

1. Navigeer naar uw **API Management-exemplaar** in de Azure Portal.
2. Selecteer **Protocol instellingen** in het menu.  
3. Gewenste protocollen of code ringen in-of uitschakelen.
4. Klik op **Opslaan**. Wijzigingen worden binnen een uur toegepast.  

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [TLS (Transport Layer Security)](/dotnet/framework/network-programming/tls).
* Bekijk meer [Video's](https://azure.microsoft.com/documentation/videos/index/?services=api-management) over API management.

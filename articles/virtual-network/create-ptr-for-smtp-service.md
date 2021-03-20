---
title: Zones voor reverse lookup configureren voor een SMTP-banner controle
titlesuffix: Azure Virtual Network
description: Hierin wordt beschreven hoe u zones voor reverse lookup configureert voor een SMTP-banner controle in azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c8ffadb8d54db0c2a99dc12e45b5990155a0505e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135058"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Zones voor reverse lookup configureren voor een SMTP-banner controle

In dit artikel wordt beschreven hoe u een zone voor reverse lookup gebruikt in Azure DNS en een reverse DNS-record (PTR) maakt voor de controle van de SMTP-banner.

## <a name="symptom"></a>Symptoom

Als u een SMTP-server in Microsoft Azure host, wordt mogelijk het volgende fout bericht weer gegeven wanneer u een bericht verzendt of ontvangt van externe e-mail servers:

**554: geen PTR-record**

## <a name="solution"></a>Oplossing

Voor een virtueel IP-adres in Azure worden de omgekeerde records gemaakt in micro soft-domein zones in eigendom, geen aangepaste domein zones.

Als u PTR-records in micro soft-zones in eigendom wilt configureren, gebruikt u de eigenschap-ReverseFqdn in de PublicIpAddress-resource. Zie [Reverse DNS configureren voor services die worden gehost in azure](../dns/dns-reverse-dns-for-azure-services.md)voor meer informatie.

Wanneer u de PTR-records configureert, moet u ervoor zorgen dat het IP-adres en de omgekeerde FQDN eigendom zijn van het abonnement. Als u probeert een omgekeerde FQDN in te stellen die niet bij het abonnement hoort, wordt het volgende fout bericht weer gegeven:

```output
Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                    
1) ReverseFqdn matches fqdn of any public ip resource under the subscription;
2) ReverseFqdn resolves to the fqdn (through CName records chain) of any public ip resource under the subscription;
3) It resolves to the ip address (through CName and A records chain) of a static public ip resource under the subscription.
```

Als u uw SMTP-banner hand matig wijzigt zodat deze overeenkomt met onze standaard omgekeerde FQDN, kan de externe e-mail server nog steeds mislukken omdat de host van de SMTP-banner mogelijk overeenkomt met de MX-record voor het domein.

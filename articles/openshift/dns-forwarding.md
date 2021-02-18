---
title: Door sturen van DNS configureren voor Azure Red Hat open Shift 4
description: Door sturen van DNS configureren voor Azure Red Hat open Shift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 68cc5dbee5b96472d11bdfb3a8bf35d118638dcd
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633866"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Door sturen via DNS configureren op een Azure Red Hat open Shift 4-cluster

Als u het door sturen van DNS wilt configureren voor een open Shift-cluster van Azure Red Hat, moet u de DNS-operator wijzigen. Met deze wijziging kan uw toepassing in het cluster worden uitgevoerd voor het omzetten van namen die worden gehost op een privé-DNS-server buiten het cluster. Deze stappen worden [hier](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)beschreven voor open Shift 4,6.

Als u bijvoorbeeld alle DNS-aanvragen voor *. example.com wilt door sturen om te worden omgezet door een DNS-server 192.168.100.10, kunt u de operator configuratie bewerken door het volgende uit te voeren:
 
```bash
oc edit dns.operator/default
```
 
Hiermee wordt een editor gestart en kunt u vervangen door `spec: {}` :
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Sla het bestand op en sluit de editor af.

## <a name="next-steps"></a>Volgende stappen
Bekijk [hier](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)meer informatie over DNS-door sturen voor open Shift 4,6.
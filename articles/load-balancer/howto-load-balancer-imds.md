---
title: Load balancer meta gegevens ophalen met behulp van de Azure Instance Metadata Service (IMDS)
titleSuffix: Azure Load Balancer
description: Leer hoe u load balancer meta gegevens kunt ophalen met behulp van de Azure Instance Metadata Service.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 35e32627eaf26eb6f73ca382f119eab61ebd8711
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417827"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Load balancer meta gegevens ophalen met behulp van de Azure Instance Metadata Service (IMDS)

## <a name="prerequisites"></a>Vereisten

* Gebruik de [nieuwste API-versie](/virtual-machines/windows/instance-metadata-service?tabs=windows#supported-api-versions) voor uw aanvraag.

## <a name="sample-request-and-response"></a>Voorbeeld aanvraag en-antwoord
> [!IMPORTANT]
> In dit voor beeld worden proxy's omzeild. U **moet** proxy's overs Laan bij het uitvoeren van een query op IMDS. Zie [proxy's](/virtual-machines/windows/instance-metadata-service?tabs=windows#proxies)voor meer informatie.
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```

### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H "Metadata:true" --noproxy "*" "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01"
```

---
### <a name="sample-response"></a>Voorbeeldantwoord

```json
{
   "loadbalancer": {
    "publicIpAddresses":[
      {
         "frontendIpAddress":"51.0.0.1",
         "privateIpAddress":"10.1.0.4"
      }
   ],
   "inboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frontendIpAddress":"2603:10e1:100:2::1:1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
   ],
   "outboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frotendIpAddress":"2603:10e1:100:2::1:1",
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
    ]
   }
}

```

## <a name="next-steps"></a>Volgende stappen
[Veelvoorkomende fout codes en stappen voor probleem oplossing](troubleshoot-load-balancer-imds.md)

Meer informatie over [Azure instance metadata service](/virtual-machines/windows/instance-metadata-service)

[Alle meta gegevens voor een exemplaar ophalen](/virtual-machines/windows/instance-metadata-service?tabs=windows#access-azure-instance-metadata-service)

[Een standaard load balancer implementeren](quickstart-load-balancer-standard-public-portal.md)


---
title: Metrische gegevens en logboeken voor de Cloud configureren voor Azure API Management zelf-hostende gateway | Microsoft Docs
description: Meer informatie over het configureren van metrische gegevens en logboeken voor Clouds voor Azure API Management zelf-hostende gateway
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: c420c62e6f8f09a2b29398590cdb4ad410e5d296
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574061"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Metrische gegevens en logboeken voor de Cloud configureren voor Azure API Management zelf-hostende gateway

In dit artikel vindt u informatie over het configureren van metrische gegevens en logboeken voor de Cloud voor de [zelf-hostende gateway](./self-hosted-gateway-overview.md).

De zelf-hostende gateway moet worden gekoppeld aan een API Management-service en vereist uitgaande TCP/IP-verbinding met Azure op poort 443. De gateway maakt gebruik van de uitgaande verbinding voor het verzenden van telemetrie naar Azure, indien dit is geconfigureerd. 

## <a name="metrics"></a>Metrische gegevens
De zelf-hostende Gateway verzendt standaard een aantal metrische gegevens via [Azure monitor](https://azure.microsoft.com/services/monitor/), hetzelfde als de beheerde gateway [in de Cloud](api-management-howto-use-azure-monitor.md). 

De functie kan worden ingeschakeld of uitgeschakeld met behulp `telemetry.metrics.cloud` van de sleutel in de ConfigMap van de gateway-implementatie. Hieronder vindt u een uitsplitsing van de beschik bare configuraties:

| Veld  | Standaard | Beschrijving |
| ------------- | ------------- | ------------- |
| telemetry.metrics.cloud  | `true` | Hiermee schakelt u logboek registratie via Azure Monitor in. Waarde kan zijn `true` , `false` . |


Hier volgt een voor beeld van een configuratie:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

De zelf-hostende Gateway verzendt momenteel de volgende metrische gegevens via Azure Monitor:

| Metrisch  | Beschrijving |
| ------------- | ------------- |
| Aanvragen  | Aantal API-aanvragen in de periode |
| Duur van gateway aanvragen | Aantal milliseconden vanaf het moment dat de gateway de aanvraag ontving tot het moment dat het antwoord volledig werd verzonden |
| Duur van back-end-aanvragen | Aantal milliseconden dat in totaal is besteed aan IO van de back-end (verbinding maken, bytes verzenden en ontvangen)  |

## <a name="logs"></a>Logboeken

De zelf-hostende Gateway verzendt momenteel geen [Diagnostische logboeken](./api-management-howto-use-azure-monitor.md#activity-logs) naar de Cloud. Het is echter mogelijk om [lokaal logboeken te configureren en persistent](how-to-configure-local-metrics-logs.md) te maken, waarbij de zelf-hostende gateway wordt geïmplementeerd. 

Als er een gateway is geïmplementeerd in de [Azure Kubernetes-service](https://azure.microsoft.com/services/kubernetes-service/), kunt u [Azure monitor voor containers](../azure-monitor/containers/container-insights-overview.md) inschakelen om logboeken te verzamelen van uw containers en deze weer geven in log Analytics. 


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de zelf-hostende Gateway vindt u in [Azure API Management zelf-hostende gateway-overzicht](self-hosted-gateway-overview.md)
* Meer informatie over het [lokaal configureren en persistent maken van Logboeken](how-to-configure-local-metrics-logs.md)

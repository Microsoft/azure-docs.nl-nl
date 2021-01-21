---
title: Azure Traffic Manager | Microsoft Docs
description: In dit artikel vindt u een overzicht van Azure Traffic Manager. Zoek uit of dit de juiste keuze is voor taak verdeling van gebruikers verkeer voor uw toepassing.
services: traffic-manager
author: duongau
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2021
ms.author: duau
ms.openlocfilehash: 09b82eed5ad6a9ad121ca56d197eb9c003d027f5
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624906"
---
# <a name="what-is-traffic-manager"></a>Wat is Traffic Manager?
Azure Traffic Manager is een op DNS gebaseerd verkeer load balancer. Met deze service kunt u verkeer distribueren naar uw open bare toepassingen in de wereld wijde Azure-regio's. Traffic Manager biedt ook uw open bare eind punten met hoge Beschik baarheid en snelle reactie tijd.

Traffic Manager maakt gebruik van DNS om de client aanvragen naar het juiste service-eind punt te sturen op basis van een verkeers routerings methode. Traffic Manager biedt ook status controle voor elk eind punt. Het eind punt kan elke Internet gerichte service zijn die binnen of buiten Azure wordt gehost. Traffic Manager biedt een scala aan [routeringsmethoden voor verkeer](traffic-manager-routing-methods.md) en [opties voor eindpuntcontrole](traffic-manager-monitoring.md) om verschillende toepassingsbehoeften en modellen voor automatische failover mogelijk te kunnen maken. Traffic Manager is bestand tegen storingen, waaronder het uitvallen van een hele Azure-regio.

>[!NOTE]
> Azure biedt een pakket volledig beheerde oplossingen voor taakverdeling voor uw scenario's. Als u op zoek bent naar beëindiging van het TLS-protocol (Transport Layer Security), ('SSL-offload') of aanvragen per HTTP/HTTPS-aanvraag, verwerking via de toepassingslaag, raadpleegt u [Application Gateway](../application-gateway/overview.md). Als u op zoek bent naar regionale taakverdeling, raadpleegt u [Load Balancer](../load-balancer/load-balancer-overview.md). Uw end-to-end scenario 's kunnen eventueel profiteren van een combinatie van deze oplossingen.
>
> Zie [Opties voor taakverdeling in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview) voor een vergelijking van de opties voor taakverdeling van Azure.

Traffic Manager biedt de volgende functies:

## <a name="increase-application-availability"></a>Beschikbaarheid van toepassingen verhogen

Traffic Manager biedt hoge beschikbaarheid voor uw essentiële toepassingen doordat uw eindpunten worden gecontroleerd en automatische failover wordt uitgevoerd als een eindpunt uitvalt.
    
## <a name="improve-application-performance"></a>Prestaties van toepassingen verbeteren

Met Azure kunt u Cloud Services en websites uitvoeren in data centers over de hele wereld. Traffic Manager kunt de reactie snelheid van uw website verbeteren door verkeer door te sturen naar het eind punt met de laagste latentie.

## <a name="service-maintenance-without-downtime"></a>Onderhoud van service zonder downtime

U kunt gepland onderhoud uitvoeren voor uw toepassingen zonder uitval tijd. Traffic Manager zorgt ervoor dat tijdens het onderhoud verkeer naar alternatieve eindpunten wordt doorgestuurd.

## <a name="combine-hybrid-applications"></a>Hybride toepassingen combineren

Traffic Manager biedt ondersteuning voor externe, niet-Azure-eindpunten zodat het kan worden gebruikt met hybride cloud- en on-premises implementaties, waaronder de [burst-to-cloud](https://azure.microsoft.com/overview/what-is-cloud-bursting/), 'migrate-to-cloud' en 'failover-to-cloud'-scenario's.

## <a name="distribute-traffic-for-complex-deployments"></a>Verkeer voor complexe implementaties verdelen

Met [geneste Traffic Manager-profielen](traffic-manager-nested-profiles.md) kunnen meerdere routeringsmethoden voor verkeer worden gecombineerd om geavanceerde en flexibele regels te maken om de behoeften van grotere, complexere implementaties te kunnen schalen.

## <a name="pricing"></a>Prijzen

Zie [Prijzen voor Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/) voor informatie over de prijzen.


## <a name="next-steps"></a>Volgende stappen

- [Een Traffic Manager-profiel maken](./quickstart-create-traffic-manager-profile.md).
- [Hoe Traffic Manager werkt](traffic-manager-how-it-works.md).
- Bekijk [frequently asked questions](traffic-manager-FAQs.md) (veelgestelde vragen) over Traffic Manager.
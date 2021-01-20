---
title: Azure Traffic Manager | Microsoft Docs
description: In dit artikel vindt u een overzicht van Azure Traffic Manager. U kunt hier zien of het de juiste keuze is voor het uitvoeren van taakverdeling voor gebruikersverkeer voor uw toepassing.
services: traffic-manager
author: duongau
manager: twooley
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: duau
ms.openlocfilehash: e2a4db1404709dadb2500df29f3f7acf8787c2b2
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185728"
---
# <a name="what-is-traffic-manager"></a>Wat is Traffic Manager?
Azure Traffic Manager is een op DNS gebaseerde load balancer waarmee u verkeer optimaal over services kunt verdelen in Azure-regio's wereldwijd, terwijl u over hoge beschikbaarheid en een hoge reactiesnelheid beschikt.

Traffic Manager maakt gebruik van DNS om aanvragen van clients door te sturen naar de meest geschikte eindpunten op basis van een methode om verkeer te routeren en van de status van de eindpunten. Een eindpunt is een internetgerichte service die binnen of buiten Azure wordt gehost. Traffic Manager biedt een scala aan [routeringsmethoden voor verkeer](traffic-manager-routing-methods.md) en [opties voor eindpuntcontrole](traffic-manager-monitoring.md) om verschillende toepassingsbehoeften en modellen voor automatische failover mogelijk te kunnen maken. Traffic Manager is bestand tegen storingen, waaronder het uitvallen van een hele Azure-regio.

>[!NOTE]
> Azure biedt een pakket volledig beheerde oplossingen voor taakverdeling voor uw scenario's. Als u op zoek bent naar beëindiging van het TLS-protocol (Transport Layer Security), ('SSL-offload') of aanvragen per HTTP/HTTPS-aanvraag, verwerking via de toepassingslaag, raadpleegt u [Application Gateway](../application-gateway/overview.md). Als u op zoek bent naar regionale taakverdeling, raadpleegt u [Load Balancer](../load-balancer/load-balancer-overview.md). Uw end-to-end scenario 's kunnen eventueel profiteren van een combinatie van deze oplossingen.
>
> Zie [Opties voor taakverdeling in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview) voor een vergelijking van de opties voor taakverdeling van Azure.

Traffic Manager biedt de volgende functies:

## <a name="increase-application-availability"></a>Beschikbaarheid van toepassingen verhogen

Traffic Manager biedt hoge beschikbaarheid voor uw essentiële toepassingen doordat uw eindpunten worden gecontroleerd en automatische failover wordt uitgevoerd als een eindpunt uitvalt.
    
## <a name="improve-application-performance"></a>Prestaties van toepassingen verbeteren

Met Azure kunt u cloudservices of websites uitvoeren in datacenters overal ter wereld. Traffic Manager verbetert de reactiesnelheid van toepassingen door verkeer te routeren naar het eindpunt met de laagste netwerklatentie voor de client.

## <a name="perform-service-maintenance-without-downtime"></a>Servicebeheer uitvoeren zonder downtime

U kunt zonder downtime geplande onderhoudswerkzaamheden op uw toepassingen uitvoeren. Traffic Manager zorgt ervoor dat tijdens het onderhoud verkeer naar alternatieve eindpunten wordt doorgestuurd.

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
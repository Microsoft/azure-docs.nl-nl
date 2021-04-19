---
title: Referentietabel voor alle Azure Security Center-aanbevelingen
description: Dit artikel bevat Azure Security Center beveiligingsaanbevelingen die u helpen bij het beveiligen en beveiligen van uw resources.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 04/06/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: e994aead1840fd3ef9b57e92cf95e94837608d7a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719129"
---
# <a name="security-recommendations---a-reference-guide"></a>Aanbevelingen voor beveiliging: een naslaggids

In dit artikel vindt u een overzicht van de aanbevelingen die u mogelijk in Azure Security Center ziet. De aanbevelingen die in uw omgeving worden weergegeven, zijn afhankelijk van de resources die u beveiligt en uw aangepaste configuratie.

Security Center aanbevelingen van de Security Center zijn gebaseerd op de [Azure Security-benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction).
Azure Security Benchmark is de door Microsoft ontworpen, Azure-specifieke set richtlijnen voor best practices voor beveiliging en naleving op basis van algemene nalevingsframeworks. Deze algemeen gerespecteerde benchmark bouwt voort op de controles van het [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) met een focus op cloudgerichte beveiliging.

Zie [Aanbevelingen oplossen in Azure Security Center](security-center-remediate-recommendations.md) voor meer informatie over hoe u kunt reageren op deze aanbevelingen.

Uw beveiligde score is gebaseerd op het aantal Security Center aanbevelingen die u hebt voltooid. Als u wilt bepalen welke aanbevelingen u het eerst wilt oplossen, bekijkt u de ernst van elke aanbeveling en de mogelijke invloed ervan op uw beveiligde score.

> [!TIP]
> Als in de beschrijving van een aanbeveling 'Geen gerelateerd beleid' wordt vermeld, is dit meestal omdat deze aanbeveling afhankelijk is van een andere aanbeveling en het _bijbehorende_ beleid. De aanbeveling 'Endpoint Protection-statusfouten moeten worden hersteld...' is bijvoorbeeld afhankelijk van de aanbeveling die controleert of er een Endpoint Protection-oplossing is _geïnstalleerd_ ('Endpoint Protection-oplossing moet worden geïnstalleerd...'). De onderliggende aanbeveling heeft _wel_ een beleid.
> Het beperken van het beleid tot alleen de basisaanbeveling vereenvoudigt het beheer van beleid.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>Aanbevelingen voor AppServices

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Aanbevelingen voor rekenkracht

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Containeraanbevelingen

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Aanbevelingen voor gegevens

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>Aanbevelingen voor IdentityAndAccess

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>IoT-aanbevelingen

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Aanbevelingen voor netwerken

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Afgeschafte aanbevelingen

|Aanbeveling|Beschrijving en gerelateerd beleid|Ernst|
|----|----|----|
|Toegang tot App Services moet worden beperkt|De toegang tot uw App Services beperken door de netwerkconfiguratie te wijzigen, om inkomend verkeer te weigeren van bereiken die te breed zijn.<br>(Gerelateerd beleid: [preview]: Toegang tot App Services moet worden beperkt)|Hoog|
|De regels voor webtoepassingen op IaaS NSG's moeten strenger worden|Beperk de netwerkbeveiligingsgroep (NSG) van uw virtuele machines die webtoepassingen uitvoeren, met NSG-regels die te veel gelden met betrekking tot webtoepassingspoorten.<br>(Gerelateerd beleid: De regels voor NSG's ten aanzien van webtoepassingen op IaaS moeten strenger worden)|Hoog|
|Beveiligingsbeleid voor pods definiëren om de aanvalsvector te verminderen door onnodige bevoegdheden voor toepassingen te verwijderen (preview)|Definieer het beveiligingsbeleid voor pods om de aanvalsvector te verminderen door onnodige bevoegdheden voor toepassingen te verwijderen. Het wordt aanbevolen beleidsregels voor pod-beveiliging te configureren zodat pods alleen toegang hebben tot resources waartoe hen toegang is toegestaan.<br>(Gerelateerd beleid: [preview]: Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services)|Normaal|
|Azure Security Center installeren voor de IoT-beveiligingsmodule om meer inzicht te krijgen in uw IoT-apparaten|Azure Security Center installeren voor de IoT-beveiligingsmodule om meer inzicht te krijgen in uw IoT-apparaten.|Beperkt|
|Uw machines moeten opnieuw worden opgestart om systeemupdates toe te passen|Start uw machines opnieuw op om de systeemupdates toe te passen en de machine vanuit beveiligingsproblemen te beveiligen. (Gerelateerd beleid: Er moeten systeemupdates op uw computers worden geïnstalleerd)|Normaal|
|De bewakingsagent moet op uw computers worden geïnstalleerd|Met deze actie wordt de bewakingsagent geïnstalleerd op de geselecteerde virtuele machines. Selecteer een werkruimte waarnaar de agent kan rapporteren. (Geen gerelateerd beleid)|Hoog|
||||

## <a name="next-steps"></a>Volgende stappen

Zie het volgende voor meer informatie over aanbevelingen:

- [Wat zijn beveiligingsbeleid, initiatieven en aanbevelingen?](security-policy-concept.md)
- [Uw beveiligingsaanbevelingen controleren](security-center-recommendations.md)

---
title: Referentietabel voor alle Azure Security Center-aanbevelingen
description: In dit artikel vindt u de aanbevelingen voor de beveiliging van Azure Security Center waarmee u uw resources kunt beschermen en beveiligen.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 03/14/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: b4fe575039c6e913ca20198f204b6529f6c482c7
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466189"
---
# <a name="security-recommendations---a-reference-guide"></a>Aanbevelingen voor beveiliging: een naslaggids

In dit artikel vindt u een overzicht van de aanbevelingen die u mogelijk in Azure Security Center ziet. De aanbevelingen die in uw omgeving worden weergegeven, zijn afhankelijk van de resources die u beveiligt en uw aangepaste configuratie.

De aanbevelingen van Security Center zijn gebaseerd op de [beveiligings benchmark van Azure](../security/benchmarks/introduction.md). Azure Security Benchmark is de door Microsoft ontworpen, Azure-specifieke set richtlijnen voor best practices voor beveiliging en naleving op basis van algemene nalevingsframeworks. Dit algemeen gerespecteerde Bench Mark bouwt voort op de besturings elementen van het [Center voor Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) met een focus op Cloud gerichte beveiliging.

Zie [Aanbevelingen oplossen in Azure Security Center](security-center-remediate-recommendations.md) voor meer informatie over hoe u kunt reageren op deze aanbevelingen.

Uw beveiligde Score is gebaseerd op het aantal Security Center aanbevelingen dat u hebt voltooid. Als u wilt bepalen welke aanbevelingen het eerst moeten worden opgelost, bekijkt u de ernst van elke aanbeveling en de mogelijke gevolgen hiervan voor uw beveiligde Score.

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

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Aanbevelingen voor netwerken

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Afgeschafte aanbevelingen

|Aanbeveling|Beschrijving en gerelateerd beleid|Ernst|Snelle oplossing ingeschakeld? ([Meer informatie](security-center-remediate-recommendations.md#quick-fix-remediation))|Resourcetype|
|----|----|----|----|----|
|**Toegang tot App Services moet worden beperkt**|De toegang tot uw App Services beperken door de netwerkconfiguratie te wijzigen, om inkomend verkeer te weigeren van bereiken die te breed zijn.<br>(Gerelateerd beleid: [preview]: Toegang tot App Services moet worden beperkt)|Hoog|N|App Service|
|**De regels voor webtoepassingen op IaaS NSG's moeten strenger worden**|De netwerk beveiligings groep (NSG) van uw virtuele machines waarop webtoepassingen worden uitgevoerd, beveiligen met NSG-regels die zich te maken hebben met betrekking tot de poorten van Web Application.<br>(Gerelateerd beleid: De regels voor NSG's ten aanzien van webtoepassingen op IaaS moeten strenger worden)|Hoog|N|Virtuele machine|
|**Beveiligingsbeleid voor pods definiëren om de aanvalsvector te verminderen door onnodige bevoegdheden voor toepassingen te verwijderen (preview)**|Definieer het beveiligingsbeleid voor pods om de aanvalsvector te verminderen door onnodige bevoegdheden voor toepassingen te verwijderen. Het wordt aanbevolen beleidsregels voor pod-beveiliging te configureren zodat pods alleen toegang hebben tot resources waartoe hen toegang is toegestaan.<br>(Gerelateerd beleid: [preview]: Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services)|Normaal|N|Compute-resources (Containers)|
|**Azure Security Center installeren voor de IoT-beveiligingsmodule om meer inzicht te krijgen in uw IoT-apparaten**|Azure Security Center installeren voor de IoT-beveiligingsmodule om meer inzicht te krijgen in uw IoT-apparaten.|Beperkt|N|IoT-apparaat|

## <a name="next-steps"></a>Volgende stappen

Zie het volgende voor meer informatie over aanbevelingen:

- [Wat zijn beveiligings beleid, initiatieven en aanbevelingen?](security-policy-concept.md)
- [Uw beveiligings aanbevelingen controleren](security-center-recommendations.md)

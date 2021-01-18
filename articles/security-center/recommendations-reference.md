---
title: Referentietabel voor alle Azure Security Center-aanbevelingen
description: In dit artikel vindt u beveiligingsaanbevelingen van Azure Security Center waarmee u uw resources kunt beveiligen.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 01/12/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 11d4830908b4e86da12cd5e40cc26b1c1b1aecbd
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133034"
---
# <a name="security-recommendations---a-reference-guide"></a>Aanbevelingen voor beveiliging: een naslaggids

In dit artikel vindt u een overzicht van de aanbevelingen die u mogelijk in Azure Security Center ziet. De aanbevelingen die in uw omgeving worden weergegeven, zijn afhankelijk van de resources die u beveiligt en uw aangepaste configuratie.

De aanbevelingen van Security Center zijn gebaseerd op aanbevolen procedures. Sommige zijn afgestemd op de **Azure Security-benchmark**, de door Microsoft ontworpen, voor Azure specifieke richtlijnen voor aanbevolen procedures voor beveiliging en naleving op basis van algemene nalevingskaders.
[Meer informatie over Azure Security-benchmark](../security/benchmarks/introduction.md).

Zie [Aanbevelingen oplossen in Azure Security Center](security-center-remediate-recommendations.md) voor meer informatie over hoe u kunt reageren op deze aanbevelingen.

Uw beveiligingsscore is gebaseerd op het aantal Security Center-aanbevelingen dat u hebt opgelost. Als u wilt bepalen welke aanbevelingen het eerst moeten worden afgehandeld, bekijkt u de ernst van elke aanbeveling en de mogelijke gevolgen hiervan voor uw beveiligingsscore.

> [!TIP]
> Als in de beschrijving van een aanbeveling 'Geen gerelateerd beleid' wordt vermeld, is dit meestal omdat deze aanbeveling afhankelijk is van een andere aanbeveling en het _bijbehorende_ beleid. De aanbeveling 'Endpoint Protection-statusfouten moeten worden hersteld...' is bijvoorbeeld afhankelijk van de aanbeveling die controleert of er een Endpoint Protection-oplossing is _geïnstalleerd_ ('Endpoint Protection-oplossing moet worden geïnstalleerd...'). De onderliggende aanbeveling heeft _wel_ een beleid.
> Het beperken van het beleid tot alleen de basisaanbeveling vereenvoudigt het beheer van beleid.

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Aanbevelingen voor rekenkracht

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

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
|**De regels voor webtoepassingen op IaaS NSG's moeten strenger worden**|De netwerkbeveiligingsgroep (NSG) van uw virtuele machines waarop webtoepassingen worden uitgevoerd, beveiligen met NSG-regels die niet streng genoeg zijn met betrekking tot poorten van webtoepassingen.<br>(Gerelateerd beleid: De regels voor NSG's ten aanzien van webtoepassingen op IaaS moeten strenger worden)|Hoog|N|Virtuele machine|
|**Beveiligingsbeleid voor pods definiëren om de aanvalsvector te verminderen door onnodige bevoegdheden voor toepassingen te verwijderen (preview)**|Definieer het beveiligingsbeleid voor pods om de aanvalsvector te verminderen door onnodige bevoegdheden voor toepassingen te verwijderen. Het wordt aanbevolen beleidsregels voor pod-beveiliging te configureren zodat pods alleen toegang hebben tot resources waartoe hen toegang is toegestaan.<br>(Gerelateerd beleid: [preview]: Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services)|Normaal|N|Compute-resources (Containers)|
|**Azure Security Center installeren voor de IoT-beveiligingsmodule om meer inzicht te krijgen in uw IoT-apparaten**|Azure Security Center installeren voor de IoT-beveiligingsmodule om meer inzicht te krijgen in uw IoT-apparaten.|Beperkt|N|IoT-apparaat|

## <a name="next-steps"></a>Volgende stappen

Zie het volgende voor meer informatie over aanbevelingen:

- [Aanbevelingen voor beveiliging in Azure Security Center](security-center-recommendations.md)
- [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)

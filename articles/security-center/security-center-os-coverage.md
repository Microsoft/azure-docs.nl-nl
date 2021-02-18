---
title: Platforms die worden ondersteund door Azure Security Center | Microsoft Docs
description: Dit document bevat een lijst met platforms die door Azure Security Center worden ondersteund.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 4cc8130d34b5f51728fb2e7fb47c1fb0717d2138
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595116"
---
# <a name="supported-platforms"></a>Ondersteunde platforms 

Op deze pagina worden de platforms en omgevingen weergegeven die door Azure Security Center worden ondersteund.

## <a name="combinations-of-environments"></a>Combinaties van omgevingen <a name="vm-server"></a>

Azure Security Center biedt ondersteuning voor virtuele machines en servers in verschillende typen hybride omgevingen:

* Alleen Azure
* Azure en on-premises
* Azure en andere clouds
* Azure, andere clouds en on-premises

Azure Security Center detecteert automatisch IaaS-resources die in het abonnement zijn geïmplementeerd voor een Azure-omgeving die wordt geactiveerd met een Azure-abonnement.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Security Center is afhankelijk van de [Log Analytics-agent](../azure-monitor/agents/agents-overview.md#log-analytics-agent). Zorg ervoor dat op de computers een van de ondersteunde besturingssystemen voor deze agent wordt uitgevoerd, zoals wordt beschreven op de volgende pagina's:

* [Log Analytics-agent voor door Windows ondersteunde besturingssystemen](../azure-monitor/agents/agents-overview.md#supported-operating-systems)
* [Log Analytics-agent voor door Linux ondersteunde besturingssystemen](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Zorg er ook voor dat uw Log Analytics-agent correct [is geconfigureerd voor het verzenden van gegevens naar Security Center](security-center-enable-data-collection.md#manual-agent)

Zie [Functiedekking voor computers](security-center-services.md) voor meer informatie over de specifieke Security Center-functies die beschikbaar zijn in Windows en Linux.

> [!NOTE]
> Azure Defender is weliswaar ontworpen om servers te beveiligen, maar de meeste mogelijkheden van **Azure Defender voor servers** worden ook ondersteund voor Windows 10-machines. Een functie die momenteel niet wordt ondersteund is [de EDR-oplossing die is geïntegreerd in Security Center: Microsoft Defender voor Eindpunt](security-center-wdatp.md).

## <a name="managed-virtual-machine-services"></a>Beheerde services voor virtuele machines <a name="virtual-machine"></a>

Virtuele machines worden ook in een klantabonnement gemaakt als onderdeel van sommige door Azure beheerde services, zoals Azure Kubernetes (AKS), Azure Databricks en meer. Security Center detecteert deze virtuele machines ook, en u kunt de Log Analytics-agent installeren en configureren als er een ondersteund besturingssysteem beschikbaar is.

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

Virtuele machines die worden uitgevoerd in een cloudservice, worden ook ondersteund. Alleen webwerkrollen in de cloudservice die worden uitgevoerd in productiesites, worden bewaakt. Zie [Overzicht van Azure Cloud Services](../cloud-services/cloud-services-choose-me.md) voor meer informatie over de cloudservice.

De beveiliging van virtuele machines die zich in Azure Stack bevinden, wordt ook ondersteund. Zie [Uw virtuele Azure Stack-machines onboarden naar Security Center](quickstart-onboard-machines.md) voor meer informatie over de integratie van Security Center met Azure Stack. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe [Security Center gegevens verzamelt met behulp van de Log Analytics-agent](security-center-enable-data-collection.md).
- Meer informatie over hoe [Security Center gegevens beheert en beveiligt](security-center-data-security.md).
- Leer de ontwerpoverwegingen kennen en leer [deze in te plannen als u de overstap naar Azure Security Center wilt maken](security-center-planning-and-operations-guide.md).
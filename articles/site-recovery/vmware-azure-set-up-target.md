---
title: Het replicatie doel voor de VMware-VM voorbereiden in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u uw doel-Azure-omgeving voorbereidt voor de replicatie van virtuele VMware-machines naar Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "73693165"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>De doel omgeving voorbereiden voor herstel na nood gevallen van virtuele VMware-machines of fysieke servers naar Azure

In dit artikel wordt beschreven hoe u uw doel-Azure-omgeving voorbereidt om virtuele VMware-machines of fysieke servers te repliceren naar Azure.

## <a name="prerequisites"></a>Vereisten

In het artikel wordt ervan uitgegaan:
- U hebt een Recovery Services kluis gemaakt op [Azure Portal](https://portal.azure.com "Azure Portal") om uw bron machines te beveiligen
- U hebt uw on-premises omgeving ingesteld voor het repliceren van de [virtuele VMware-machines](vmware-azure-set-up-source.md) of [fysieke servers](physical-azure-set-up-source.md) naar Azure.

## <a name="prepare-target"></a>Doel voorbereiden

Na het volt ooien van de **stap 1: beveiligings doel selecteren** en **stap 2: bron voorbereiden**, gaat u naar **stap 3: doel**

![Doel voorbereiden](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Abonnement:** Selecteer in de vervolg keuzelijst het abonnement waarnaar u de virtuele machines of fysieke servers wilt repliceren.
2. **Implementatie model:** Het implementatie model selecteren (klassiek of Resource Manager)

Op basis van het gekozen implementatie model wordt een validatie uitgevoerd om ervoor te zorgen dat u ten minste één virtueel netwerk hebt in het doel abonnement om uw virtuele machine of fysieke server naar te repliceren en er een failover voor uit te voeren.

Zodra de validaties zijn voltooid, klikt u op OK om naar de volgende stap te gaan.

Als u geen virtueel netwerk hebt, kunt u er een maken door te klikken op de knop **+ netwerk** boven aan de pagina.

## <a name="next-steps"></a>Volgende stappen
[Configureer de replicatie-instellingen](vmware-azure-set-up-replication.md).

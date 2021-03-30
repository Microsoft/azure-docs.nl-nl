---
title: De doel omgeving instellen voor fysieke servers in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u de Azure-doel omgeving instelt voor herstel na nood gevallen van fysieke servers met behulp van Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "73953904"
---
# <a name="prepare-target-vmware-to-azure"></a>Doel voorbereiden (VMware naar Azure)

In dit artikel wordt beschreven hoe u uw Azure-omgeving voorbereidt om te beginnen met het repliceren van fysieke servers (x64) waarop Windows of Linux wordt uitgevoerd in Azure.

## <a name="prerequisites"></a>Vereisten

In het artikel wordt ervan uitgegaan:
- U hebt een Recovery Services kluis gemaakt om uw fysieke servers te beschermen. U kunt een Recovery Services kluis maken op basis van de [Azure Portal](https://portal.azure.com "Azure Portal").
- U hebt [uw on-premises omgeving ingesteld voor het repliceren van](physical-azure-disaster-recovery.md) fysieke servers naar Azure.

## <a name="prepare-target"></a>Doel voorbereiden

Na het volt ooien van de **stap 1: beveiligings doel selecteren** en **stap 2: bron voorbereiden**, gaat u naar **stap 3: doel**

![Doel voorbereiden](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Abonnement:** Selecteer in de vervolg keuzelijst het abonnement waarnaar u de fysieke servers wilt repliceren.
2. **Implementatie model:** Het implementatie model selecteren (klassiek of Resource Manager)

Op basis van het gekozen implementatie model wordt een validatie uitgevoerd om ervoor te zorgen dat u ten minste één compatibel opslag account en een virtueel netwerk in het doel abonnement hebt om uw fysieke servers te repliceren en te failoveren naar.

Zodra de validaties zijn voltooid, klikt u op OK om naar de volgende stap te gaan.

Als u geen compatibel Resource Manager-opslag account of virtueel netwerk hebt, kunt u er een maken door te klikken op het **+ opslag account** of **+ netwerk** knoppen boven aan de pagina.

## <a name="next-steps"></a>Volgende stappen
[Configureer de replicatie-instellingen](vmware-azure-set-up-replication.md).

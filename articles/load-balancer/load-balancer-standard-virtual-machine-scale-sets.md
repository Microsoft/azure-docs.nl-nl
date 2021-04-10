---
title: Regels toevoegen voor Azure Standard Load Balancer-en virtuele-machine schaal sets
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: Met dit leer traject kunt u aan de slag met Azure Standard Load Balancer en virtuele-machine schaal sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592276"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>Regels toevoegen voor Azure Load Balancer met schaal sets voor virtuele machines

Houd rekening met de volgende richt lijnen wanneer u werkt met schaal sets voor virtuele machines en Azure Load Balancer.

## <a name="port-forwarding-and-inbound-nat-rules"></a>Poort door sturen en binnenkomende NAT-regels

Nadat de schaalset is gemaakt, kan de back-end-poort niet worden gewijzigd voor een taakverdelings regel die wordt gebruikt door een status test van de load balancer. Als u de poort wilt wijzigen, verwijdert u de status test door de schaalset voor de virtuele machine bij te werken en de poort bij te werken. Configureer de status test vervolgens opnieuw.

Wanneer u de schaalset voor virtuele machines in de back-end-pool van de load balancer gebruikt, worden de standaard binnenkomende NAT-regels automatisch gemaakt.
  
## <a name="inbound-nat-pool"></a>Binnenkomende NAT-pool

Elke schaalset voor virtuele machines moet ten minste één binnenkomende NAT-groep hebben. Een binnenkomende NAT-pool is een verzameling van binnenkomende NAT-regels. Eén binnenkomende NAT-groep kan geen meerdere virtuele-machine schaal sets ondersteunen.

## <a name="load-balancing-rules"></a>Taakverdelingsregels

Wanneer u de schaalset voor virtuele machines in de back-end-pool van de load balancer gebruikt, wordt automatisch de standaard regel voor taak verdeling gemaakt.
  
## <a name="outbound-rules"></a>Regels voor uitgaand verkeer

Als u een uitgaande regel wilt maken voor een back-end-pool waarnaar al wordt verwezen door een taakverdelings regel, selecteert u **Nee** onder **impliciete uitgaande regels maken** in het Azure Portal wanneer de regel voor binnenkomende taak verdeling wordt gemaakt.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Scherm opname van het maken van een regel voor taak verdeling." border="true":::

Gebruik de volgende methoden voor het implementeren van een schaalset voor virtuele machines met een bestaand exemplaar van Load Balancer:

* [Een schaalset voor virtuele machines configureren met een bestaand exemplaar van Azure Load Balancer met behulp van de Azure Portal](./configure-vm-scale-set-portal.md)
* [Een schaalset voor virtuele machines configureren met een bestaand exemplaar van Azure Load Balancer met behulp van Azure PowerShell](./configure-vm-scale-set-powershell.md)
* [Een schaalset voor virtuele machines configureren met een bestaand exemplaar van Azure Load Balancer met behulp van de Azure CLI](./configure-vm-scale-set-cli.md)
* [Een bestaand exemplaar van Azure Load Balancer dat wordt gebruikt door een virtuele-machine schaalset bijwerken of verwijderen](./update-load-balancer-with-vm-scale-set.md)

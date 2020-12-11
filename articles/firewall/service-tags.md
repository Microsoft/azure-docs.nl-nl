---
title: Overzicht van Azure Firewall-service Tags
description: Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels die het maken van beveiligingsregel vereenvoudigt.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 83e9a96573bbc72e0afff61cc0f151f95b081e30
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031576"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall-service Tags

Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels die het maken van beveiligingsregel vereenvoudigt. U kunt niet uw eigen servicetag maken en ook niet opgeven welke IP-adressen zijn opgenomen in een tag. Microsoft beheert de adresvoorvoegsels die de servicetag omvat en werkt de servicetag automatisch bij wanneer adressen veranderen.

Azure Firewall service tags kunnen worden gebruikt in het doel veld netwerk regels. U kunt deze gebruiken in plaats van specifieke IP-adressen.

## <a name="supported-service-tags"></a>Ondersteunde service Tags

Zie [service tags van het virtuele netwerk](../virtual-network/service-tags-overview.md#available-service-tags) voor een lijst met Service tags die beschikbaar zijn voor gebruik in azure firewall-netwerk regels.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure firewall logica voor regel verwerking](rule-processing.md)voor meer informatie over Azure firewall-regels.

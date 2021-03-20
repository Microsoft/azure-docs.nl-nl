---
title: Azure Analysis Services configureren voor VNet-gegevens bronnen | Microsoft Docs
description: Informatie over het configureren van een Azure Analysis Services-server voor het gebruik van een gateway voor gegevens bronnen op Azure Virtual Network (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 912dab16cd854ae940b7bbdfe88a8da7adf1c5e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84197201"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Gateway gebruiken voor gegevensbronnen in een virtueel Azure-netwerk (VNet)

In dit artikel wordt de Azure Analysis Services eigenschap **AlwaysUseGateway** voor het gebruik van gegevens bronnen op een [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)beschreven.

## <a name="server-access-to-vnet-data-sources"></a>Server toegang tot VNet-gegevens bronnen

Als uw gegevens bronnen toegankelijk zijn via een VNet, moet uw Azure Analysis Services-server verbinding maken met deze gegevens bronnen alsof ze on-premises zijn, in uw eigen omgeving. U kunt de **AlwaysUseGateway** -Server eigenschap configureren om de server op te geven voor toegang tot alle gegevens bronnen via een [on-premises gateway](analysis-services-gateway.md). 

Gegevens bronnen van Azure SQL Managed instance worden uitgevoerd in azure VNet met een privé-IP-adres. Als het open bare eind punt is ingeschakeld voor het exemplaar, is een gateway niet vereist. Als het open bare eind punt niet is ingeschakeld, is een on-premises gegevens gateway vereist en moet de eigenschap AlwaysUseGateway worden ingesteld op True.

> [!NOTE]
> Deze eigenschap is alleen effectief wanneer een [on-premises gegevens gateway](analysis-services-gateway.md) is geïnstalleerd en geconfigureerd. De gateway kan zich in het VNet bevindt.

## <a name="configure-alwaysusegateway-property"></a>Eigenschap AlwaysUseGateway configureren

1. Selecteer in SSMS > server > **Eigenschappen**  >  **Algemeen** **de optie Geavanceerde eigenschappen (alle) weer geven**.
2. Selecteer **waar** in de **ASPaaS\AlwaysUseGateway**.

    ![Altijd gateway-eigenschap gebruiken](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Zie ook
[Verbinding maken met on-premises gegevens bronnen](analysis-services-gateway.md)   
[Een on-premises gegevens gateway installeren en configureren](analysis-services-gateway-install.md)   
[Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   


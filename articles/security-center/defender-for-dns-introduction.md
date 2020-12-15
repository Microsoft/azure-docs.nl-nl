---
title: Azure Defender voor DNS - de voordelen en functies
description: Meer informatie over de voordelen en functies van Azure Defender voor DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 18d9cb3c64eb41b33a58b248f3826bd9a80c6d90
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754803"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Inleiding tot Azure Defender voor DNS

[Azure DNS](../dns/dns-overview.md) is een service voor het hosten van DNS-domeinen die naamomzetting verzorgt met behulp van de Microsoft Azure-infrastructuur. Door uw domeinen in Azure te hosten, kunt u uw DNS-records met dezelfde referenties, API's, hulpprogramma's en facturering beheren als voor uw andere Azure-services.

Azure Defender voor DNS biedt een extra beveiligingslaag voor uw cloudresources door:

- continu alle DNS-query's van uw Azure-resources te bewaken
- geavanceerde beveiligingsanalyses uit te voeren om u te waarschuwen voor verdachte activiteit

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Preview<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Prijzen:|**Azure Defender voor DNS** wordt gefactureerd zoals wordt weergegeven op [de pagina met prijzen](security-center-pricing.md)|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Nee](./media/icons/no-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Wat zijn de voordelen van Azure Defender voor DNS?

Azure Defender voor DNS beschermt tegen problemen, waaronder:

- Gegevensexfiltratie van uw Azure-resources met behulp van DNS-tunneling
- Malware die communiceert met C&C-server
- Communicatie met schadelijke domeinen als phishing en crypto mining
- DNS-aanvallen - communicatie met schadelijke DNS-omzetters 

Een volledige lijst van de waarschuwingen die door Azure Defender voor DNS worden verschaft, vindt u op de [pagina met waarschuwingen](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Afhankelijkheden

Azure Defender voor DNS gebruikt geen agents. 

Als u uw DNS-laag wilt beveiligen, schakelt u Azure Defender voor DNS in voor elk van uw abonnementen, zoals wordt beschreven in [Azure Defender inschakelen](security-center-pricing.md).


## <a name="next-steps"></a>Volgende stappen

In dit artikel bent u meer te weten gekomen over Azure Defender voor DNS. Raadpleeg het volgende artikel voor gerelateerd materiaal: 

- Beveiligingswaarschuwingen kunnen worden gegenereerd door Security Center of door Security Center worden ontvangen vanuit andere beveiligingsproducten. Als u al deze waarschuwingen wilt exporteren naar Azure Sentinel, een externe SIEM of een ander extern hulpprogramma, volgt u de instructies in [Waarschuwingen naar een SIEM exporteren](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Azure Defender inschakelen](security-center-pricing.md)
---
title: Veelgestelde vragen over Analysis Services netwerk verbinding | Microsoft Docs
description: In dit artikel vindt u antwoorden op enkele veelgestelde vragen over Analysis Services netwerk verbinding.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82838498"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>Veelgestelde vragen over de netwerkverbinding van Analysis Services

In dit artikel vindt u antwoorden op veelgestelde vragen over het maken van verbinding met opslag accounts, gegevens bronnen, firewalls en IP-adressen.

## <a name="backup-and-restore"></a>Back-ups en herstellen

**Vraag** : hoe kan ik back-ups maken en herstellen met behulp van een opslag account dat zich achter een firewall bevindt?   
**Antwoord** -Azure Analysis Services maakt geen gebruik van vaste IP-adressen of service tags. Het bereik van de IP-adressen die uw Analysis Services-servers gebruiken, kunnen alles in het bereik van IP-adressen voor de *Azure-regio* zijn. Omdat de IP-adressen van uw server een variabele zijn en na verloop van tijd kunnen worden gewijzigd, moeten uw firewall regels het hele bereik van IP-adressen van Azure-regio's toestaan waarop uw server zich bevindt.

**Vraag** : mijn Azure-opslag account bevindt zich in een andere regio dan mijn Analysis Services-server. Hoe kan ik Firewall-instellingen voor opslag account configureren?   
**Antwoord** : als het opslag account zich in een andere regio bevindt, configureert u Firewall-instellingen voor het opslag account om toegang vanuit **geselecteerde netwerken** toe te staan. Geef in het **adres bereik** van de firewall het IP-adres bereik op voor de regio waarin de Analysis Services-server zich bevindt. Als u de IP-bereiken voor Azure-regio's wilt ophalen, raadpleegt u [Azure IP-bereiken en service Tags – open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Het configureren van Firewall instellingen voor opslag accounts om toegang vanaf alle netwerken toe te staan, wordt ondersteund, maar het kiezen van geselecteerde netwerken en het opgeven van een IP-adres bereik verdient de voor keur. 

**Vraag** : mijn Azure-opslag account bevindt zich in dezelfde regio als mijn Analysis Services-server. Hoe kan ik Firewall-instellingen voor het opslag account configureren?   
**Antwoord** : omdat uw Analysis Services-server en-opslag account zich in dezelfde regio bevinden, gebruiken de communicatie tussen de servers interne IP-adresbereiken, daarom kunt u een firewall configureren om geselecteerde netwerken te gebruiken en een IP-adres bereik op te geven niet wordt ondersteund. Als er voor het organisatie beleid een firewall is vereist, moet deze zijn geconfigureerd om toegang vanaf alle netwerken toe te staan.


## <a name="data-source-connections"></a>Gegevens bron verbindingen

**Vraag** : Ik heb een VNET voor mijn gegevens bron systeem. Hoe kan ik mijn Analysis Services-servers toegang geven tot de data base vanuit het VNET?   
**Antwoord** -Azure Analysis Services kan geen verbinding maken met een VNET. De beste oplossing is hier om een on-premises gegevens gateway op het VNET te installeren en configureren en vervolgens uw Analysis Services-servers te configureren met de **AlwaysUseGateway** -Server eigenschap. Zie voor meer informatie [gateway gebruiken voor gegevens bronnen op een Azure Virtual Network (VNet)](analysis-services-vnet-gateway.md).

**Vraag** : Ik heb een bron database achter een firewall. Hoe kan ik de firewall zo configureren dat mijn Analysis Services-server er toegang toe heeft?   
**Antwoord** -Azure Analysis Services maakt geen gebruik van vaste IP-adressen of service tags. Het bereik van de IP-adressen die uw Analysis Services-servers gebruiken, kunnen alles in het bereik van IP-adressen voor de *Azure-regio* zijn. U moet het *volledige bereik* van IP-adressen opgeven voor de Azure-regio van uw server in de firewall regels van de bron database. Een andere en mogelijk veiliger, alternatief is het configureren van een on-premises gegevens gateway. U kunt vervolgens uw Analysis Services-servers configureren met de [AlwaysUseGateway-Server eigenschap](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)en ervoor zorgen dat de on-premises gegevens gateway een IP-adres heeft dat is toegestaan door de firewall regels van de gegevens bron.

## <a name="azure-apps-with-ip-address"></a>Azure-apps met IP-adres

**Vraag** : Ik gebruik een toepassing op basis van Azure (bijvoorbeeld Azure Functions, Azure Data Factory) met een IP-adres dat onderweg verandert. Hoe kan ik de firewall regels van Azure Analysis Services beheren om het IP-adres waarop mijn app wordt uitgevoerd, dynamisch toe te staan?   
**Antwoord** -Azure Analysis Services biedt geen ondersteuning voor persoonlijke koppelingen, VNETs of service tags. Er zijn een aantal open source-oplossingen (bijvoorbeeld https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) waarmee het IP-adres van de client toepassing wordt gedetecteerd en de firewall regels automatisch en tijdelijk worden bijgewerkt.


## <a name="next-steps"></a>Volgende stappen

[Een on-premises gegevens gateway installeren en configureren](analysis-services-gateway-install.md)   
[Verbinding maken met on-premises gegevens bronnen met on-premises gegevens gateway](analysis-services-gateway.md)   
[Gateway gebruiken voor gegevensbronnen in een virtueel Azure-netwerk (VNet)](analysis-services-vnet-gateway.md)

---
title: Azure Firewall gebruiken om virtueel bureau blad van Windows te beveiligen
description: Meer informatie over het gebruik van Azure Firewall voor het beveiligen van Windows Virtual Desktop-implementaties
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: d5320f44aa5d922cea852ab09e5141fad277e2b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566023"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Azure Firewall gebruiken om Windows Virtual Desktop-implementaties te beveiligen

Virtueel bureau blad van Windows is een desktop-en app Virtualization-service die wordt uitgevoerd op Azure. Wanneer een eind gebruiker verbinding maakt met een virtueel-bureaublad omgeving van Windows, wordt de sessie uitgevoerd door een hostgroep. Een hostgroep is een verzameling virtuele machines van Azure die zich registreren bij Windows Virtual Desktop als hosts voor sessies. Deze virtuele machines worden uitgevoerd in uw virtuele netwerk en zijn onderhevig aan de beveiligings controles van het virtuele netwerk. Ze hebben uitgaande internet toegang tot de virtueel bureau blad-service van Windows nodig om goed te kunnen werken en hebben mogelijk ook uitgaande internet toegang nodig voor eind gebruikers. Azure Firewall kunt u helpen uw omgeving te vergren delen en uitgaand verkeer te filteren.

[![Architectuur ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) van Windows virtueel bureau blad](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Volg de richt lijnen in dit artikel om extra beveiliging te bieden voor uw Windows Virtual Desktop-hostgroep met Azure Firewall.

## <a name="prerequisites"></a>Vereisten


 - Een geïmplementeerde virtuele bureaublad omgeving van Windows en een hostgroep.

   Zie [zelf studie: een hostgroep maken met behulp van Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) en [een hostgroep met een Azure Resource Manager sjabloon maken](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md)voor meer informatie.

Zie [Windows Virtual Desktop Environment](../virtual-desktop/environment-setup.md)(Engelstalig) voor meer informatie over virtuele-bureaublad omgevingen van Windows.

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Uitgaande toegang van hostgroep tot virtueel bureau blad van Windows

De virtuele Azure-machines die u voor virtuele Windows-Bureau bladen maakt, moeten toegang hebben tot verschillende FQDN-namen (FULLy Qualified Domain names) om goed te kunnen functioneren. Azure Firewall biedt een FQDN-tag voor het virtuele Windows-bureau blad om deze configuratie te vereenvoudigen. Gebruik de volgende stappen om uitgaand verkeer van het virtueel bureau blad-platform van Windows toe te staan:

- Implementeer Azure Firewall en configureer de door de gebruiker gedefinieerde route (UDR) van uw Windows-host voor virtuele bureau blad om al het verkeer via de Azure Firewall te routeren. Uw standaard route wijst nu naar de firewall.
- Maak een toepassings regel verzameling en voeg een regel toe om de *WindowsVirtualDesktop* FQDN-tag in te scha kelen. Het bron-IP-adres bereik is het virtuele netwerk van de hostgroep, het protocol is **https** en de doel locatie is **WindowsVirtualDesktop**.

- De set vereiste opslag-en service bus-accounts voor uw Windows Virtual Desktop-hostgroep is specifiek voor implementatie, zodat deze nog niet is vastgelegd in de WindowsVirtualDesktop FQDN-tag. U kunt dit op een van de volgende manieren aanpakken:

   - Sta https-toegang vanuit het subnet van uw hostgroep toe aan * xt.blob.core.windows.net, * eh.servicebus.windows.net en * xt.table.core.windows.net. Deze FQDN-joker tekens bieden de vereiste toegang, maar zijn minder beperkend.
   - Gebruik de volgende log Analytics-query om de exacte vereiste FQDN-namen weer te geven en sta ze expliciet toe in uw firewall toepassings regels:
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- Een verzameling voor netwerk regels maken Voeg de volgende regels toe:

   - DNS toestaan: verkeer toestaan van uw particuliere IP-adres toevoegen aan * voor TCP-en UDP-poorten 53.
   - KMS toestaan: verkeer van uw virtuele Windows-bureau blad-machines naar de Windows Activation service TCP-poort 1688 toestaan. Zie [Windows-activering mislukt in scenario met geforceerde tunneling](/troubleshoot/azure/virtual-machines/custom-routes-enable-kms-activation#solution)voor meer informatie over de doel-IP-adressen.

> [!NOTE]
> Voor sommige implementaties zijn mogelijk geen DNS-regels nodig, bijvoorbeeld Azure Active Directory domein controllers sturen DNS-query's door naar Azure DNS op 168.63.129.16.

## <a name="host-pool-outbound-access-to-the-internet"></a>Uitgaande toegang tot Internet van hostgroep

Afhankelijk van de behoeften van uw organisatie wilt u mogelijk beveiligde uitgaande internet toegang inschakelen voor uw eind gebruikers. In gevallen waarin de lijst met toegestane bestemmingen goed is gedefinieerd (bijvoorbeeld [Microsoft 365 toegang](/microsoft-365/enterprise/microsoft-365-ip-web-service)) kunt u Azure firewall toepassing en netwerk regels gebruiken om de vereiste toegang te configureren. Hierdoor stuurt het verkeer van eind gebruikers rechtstreeks naar het Internet voor de beste prestaties.

Als u het Internet verkeer van de uitgaande gebruiker wilt filteren met behulp van een bestaande on-premises beveiligde webgateway, kunt u webbrowsers of andere toepassingen die worden uitgevoerd op de Windows Virtual Desktop hostgroep configureren met een expliciete proxy configuratie. Zie bijvoorbeeld [micro soft Edge-opdracht regel opties gebruiken voor het configureren van proxy-instellingen](/deployedge/edge-learnmore-cmdline-options-proxy-settings). Deze proxy instellingen zijn alleen van invloed op de Internet toegang van uw eind gebruiker, waardoor het verkeer van het virtueel-bureaublad platform van Windows rechtstreeks via Azure Firewall wordt toegestaan.

## <a name="additional-considerations"></a>Aanvullende overwegingen

U moet mogelijk aanvullende firewall regels configureren, afhankelijk van uw vereisten:

- NTP-server toegang

   Virtuele machines met Windows maken standaard verbinding met time.windows.com via UDP-poort 123 voor tijd synchronisatie. Maak een netwerk regel om deze toegang toe te staan of voor een tijd server die u in uw omgeving gebruikt.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over virtueel bureau blad van Windows: [Wat is virtueel bureau blad van Windows?](../virtual-desktop/overview.md)